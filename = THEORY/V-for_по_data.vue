<template>
  <div id="app">
    <div>
      <div v-for="(field, key, ind) in forms" :key="ind" class="forms__field">
        <h2 v-if="(field.name !== 'password confirm') || registration"
            :class="{
              'error': $v.forms[key].value.$error || forms[key].isDirty,
              'valid': !$v.forms[key].value.$invalid && forms[key].value.length > 0
            }"
        >
          {{field.name}}
        </h2>

<!--        type="password"   (!)-->
        <input type="text"
               v-model="field.value"
               :placeholder="field.placeholder"
               v-mask="field.mask ? field.mask : ''"
               @blur="$v.forms[key].value.$touch()"
               v-if="(field.name !== 'password confirm') || registrationInterface"
        >
<!--     v-if для того, что бы одно из полей (подтверждение пароля, 'password confirm'), появлялось только при включении registrationInterface-->
      </div>

      <div @click="onRegistrationIsDone" class="btn">Take it</div>
    </div>
    <hr>
    <hello-world></hello-world>
  </div>
</template>
тз
<script lang="ts">
import Vue from "vue"
import {LoginForms} from "@/types/auth"
// @ts-ignore
import AwesomeMask from 'awesome-mask'
import {minLength, required, sameAs} from 'vuelidate/lib/validators'
import {isPhone, isPassword} from '@/utils/validation.ts'

import HelloWorld from "@/components/V-for_at_<input>[ ].vue";


export default Vue.extend({
  components: {
    HelloWorld
  },
  data: () => ({
    forms: {
      login: {
        name: 'login',
        value: '',
        placeholder: '(906) 075-19-75',
        mask: '(999) 999-99-99',
        isDirty: false
      },
      password: {
        name: 'password',
        value: '',
        placeholder: 'at least 3 signs',
        isDirty: false
      },
      passwordConfirm: {
        name: 'password confirm',
        value: '',
        placeholder: 'it must be the same as the password',
        isDirty: false
      }
    } as LoginForms,
    registrationInterface: true
  }),
  validations: {
    forms: {
      login: {
        value: {isPhone, required}
      },
      password: {
        value: {minLength: minLength(3), required, isPassword}
      },
      passwordConfirm: {
        value: {
          // @ts-ignore
          sameAs: sameAs(function (): string {
            // @ts-ignore
            return this.forms.password.value
          })
        }
      }
    }
  },
  methods: {
    onRegistrationIsDone(): void {
      console.log('onRegistrationIsDone ======')
      //Если пытаемся отправить, но поле - пустое, то отмечаем незаполненное поле красным.
      //Проверяем, заполнено ли поле. Если заполнено - даем разрешение на отправку формы: присуждаем isDirty true.
      for (let formValue of Object.values(this.forms)) {
        if (!formValue.value.length)
          formValue.isDirty = true
      }

      //посылаем запрос на регистрацию
      let isNoError = !this.$v.forms.$anyError
      if (isNoError && this.forms.login.isDirty && this.forms.password.isDirty) {

      }


    }
  },
  directives: {
    'mask': AwesomeMask
  }
})
</script>

<style lang="scss">
.error {
  color: red;
}

.valid {
  color: green;
}

.btn {
  margin-top: 20px;
  cursor: pointer;

  &:hover{
    color: grey;
  }
}

</style>
