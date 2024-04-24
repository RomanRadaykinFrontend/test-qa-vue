<template>
  <div class="wraaper">
    <h3>Форма входа</h3>
    <el-form ref="form" :rules="rules" label-position="top" :model="model">
      <div v-for="el in sortedElems" :key="el.id">
        <el-form-item :class="el.wrpClass" :label="el.label" :prop="el.prop">
          <el-input :clearable="el.isPass" :type="el.isPass ? 'password' : ''" :class="el.class" v-model="model[el.prop as string]"></el-input>
        </el-form-item>
      </div>
    </el-form>
    <el-button @click="login">Войти</el-button>
  </div>
</template>

<script lang="ts" setup>
import {ref} from "vue";
import {ElNotification, ElForm, ElFormItem, ElInput, ElButton} from "element-plus";

const createClass = (str: string) => {
  return `${str}${Math.random()}`
}

const elements = [
  {
    id: Math.random(),
    label: 'Лагин',
    wrpClass: createClass('login-wrapper'),
    class: createClass('login'),
    prop: 'login',
    isPass: false,
    isHidden: false
  },
  {
    id: Math.random(),
    label: 'Лагин',
    wrpClass: `${createClass('login-wrapper')} none`,
    class: createClass('login'),
    prop: 'login', isPass: false, isHidden: true
  },
  {
    id: Math.random(),
    label: 'Пароль',
    wrpClass: createClass('password-wrapper'),
    class: createClass('password'),
    prop: 'password', isPass: true, isHidden: false
  },
  {
    id: Math.random(),
    label: 'Пароль',
    wrpClass: `${createClass('password-wrapper')} none`,
    class: createClass('password'),
    prop: 'password', isPass: true, isHidden: true
  }
]

const sortedElems = elements.sort((a,b) => a.id - b.id)


const correctPass = '!!vk-forever12'
const regexDict = {
  phone: /^\+?\d{5,15}$/,
  email: /^(?:[a-z0-9!#$%&'*+/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+/=?^_`{|}~-]+)*|"(?:[\x01-\x08\x0B\x0C\x0E-\x1F\x21\x23-\x5B\x5D-\x7F]|\\[\x01-\x09\x0B\x0C\x0E-\x7F])*")@(?:(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?|\[(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?|[a-z0-9-]*[a-z0-9]:(?:[\x01-\x08\x0B\x0C\x0E-\x1F\x21-\x5A\x53-\x7F]|\\[\x01-\x09\x0B\x0C\x0E-\x7F])+)\])$/i
};
const model = ref({
  login: '',
  password: ''
})

const form = ref<null | HTMLFormElement>(null)

const validateLog = (rule: any, value: any, callback: any) => {
  if (!value || value.trim === ''){
    callback(new Error('Обязательное поле'))
  }
  else if (!regexDict.phone.test(value) && !regexDict.email.test(value)) {
    callback(new Error('Логин должен быть телефоном или email'))
  } else{
    callback()
  }
}
const validatePass = (rule: any, value: any, callback: any) => {
  if (value === '') {
    callback(new Error('Обязательное поле'))
  } else if (value.length < 8) {
    callback(new Error("Пароль должен быть не менее 8 символов"))
  } else {
    callback()
  }
}

const login = () => {
  if(form.value){
    form.value.validate(() => {
      if(model.value.password === correctPass){
        ElNotification({
          title: 'Успехх!',
          message: 'Вы вошли успешно',
          type: 'success',
        })
        model.value.login = ''
        model.value.password = ''
        if(form.value){
          form.value.clearValidate()
        }

      } else {
        ElNotification({
          title: 'Неудача!',
          message: 'Логин или пароль неверне',
          type: 'error',
        })
      }
    })
  }
}

const rules = {
  login: [
    {
      required: true,
      trigger: 'change',
      validator: validateLog
    }
  ],
  password: [
    {
      required: true,
      trigger: 'change',
      validator: validatePass
    }
  ],
}

</script>
<style>
.wraaper{
  padding: 32px;
  box-shadow: 0px 1px 8px -1px rgba(25, 31, 48, 0.20);
  margin: 60px auto;
}
.el-form {
  width: 400px;
}
.el-button{
  margin-top: 30px;
}
.none{
  display: none !important;
}
</style>
