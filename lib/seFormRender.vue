<template>
  <div class="page-formRender">
    <form data-vv-scope="auto-form">
      <template v-for="(items, index) in data">
        <group v-if="isArray(items)" :key="index">
          <template v-for="(item, index) in items">
            <cell v-if="item.type === 'fieldInput'" :key="index">
              <div class="content field" slot="title">
                <p class="input">
                  <label class="label" :class="{require: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model">{{item.label}}</label>
                  <input :disabled="item.disabled" v-model="item.value" v-validate="item.validate" :class="{'input': true, 'is-danger': errorv.has('auto-form.' + item.model) }" :name="item.model" type="text" :placeholder="item.placeholder || ('请输入' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" @change="onInputChange(item)">
                </p>
                <p v-show="errorv.has('auto-form.' + item.model)" class="help is-danger">{{ errorv.first('auto-form.' + item.model) }}</p>
              </div>
            </cell>
            <cell v-if="item.type === 'passwordInput'" :key="index">
              <div class="content field" slot="title">
                <p class="password-input">
                  <label class="label" :class="{require: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model">{{item.label}}</label>
                  <input key="1" v-if="!item.showPassword" :disabled="item.disabled" v-model="item.value" v-validate="item.validate" :class="{'input': true, 'is-danger': errorv.has('auto-form.' + item.model) }" :name="item.model" type="password" :placeholder="item.placeholder || ('请输入' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" @change="onInputChange(item)">
                  <input key="2" v-if="item.showPassword" :disabled="item.disabled" v-model="item.value" v-validate="item.validate" :class="{'input': true, 'is-danger': errorv.has('auto-form.' + item.model) }" :name="item.model" type="text" :placeholder="item.placeholder || ('请输入' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" @change="onInputChange(item)">
                  <a @click="onPasswordInputClick(item)">{{item.showPassword? '隐藏': '显示'}}</a>
                </p>
                <p v-show="errorv.has('auto-form.' + item.model)" class="help is-danger">{{ errorv.first('auto-form.' + item.model) }}</p>
              </div>
            </cell>
            <cell v-else-if="item.type === 'radioInput'" :key="index">
              <div class="content radio" slot="title">
                <p style="margin: 8px 0px;">
                  <label class="label" :class="{require: item.validate&&item.validate.indexOf('required') !== -1}">{{item.label}}</label>
                  <span style="display: inline-block; line-height: 1em;">
                    <label class="radio" v-for="(ite, index) in item.options" :key="index">
                      <input :disabled="item.disabled" v-model="item.value" :name="item.model" v-validate="item.validate" :value="ite.value" type="radio" @click="item.onClick?item.onClick(ite.value):''">{{ite.name}}
                    </label>
                  </span>
                  <p class="help is-danger" v-show="errorv.has('auto-form.'+item.model)">{{ errorv.first('auto-form.'+item.model) }}</p>
              </div>
            </cell>
            <cell v-else-if="item.type === 'checkBox'" :key="index">
              <div class="content radio" slot="title">
                <p>
                  <label class="label" :class="{require: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model">{{item.label}}</label>
                  <br>
                  <label class="radio" v-for="(ite, index) in item.options" :key="index">
                    <input :disabled="item.disabled" v-model="item.value" :name="item.model" v-validate="item.validate" :value="ite.value" type="checkbox">{{ite.name}}
                  </label>
                </p>
                <p class="help is-danger" v-show="errorv.has('auto-form.'+item.model)">{{ errorv.first('auto-form.'+item.model) }}</p>
              </div>
            </cell>
            <div v-else-if="item.type === 'listSelecter'" :key="index" class="content">
              <p style="margin: 0;">
                <label class="label" :class="{require2: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model"></label>
                <selector data-vv-scope="auto-form" :class="'needsclick selecter '+(item.readonly?'listReadonly':'listEditable')+(item.value==='-1'?' listPlaceHolder':'')" :readonly="item.readonly" v-validate="item.validate" :placeholder="item.placeholder || ('请选择' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" :title="item.label" :name="item.model" :options="item.options" v-model="item.value" @on-change="onSelectorChange(item)"></selector>
              </p>
              <p style="margin-left: 15px; margin-bottom: 5px;" v-show="errorv.has('auto-form.'+item.model)" class="help is-danger">{{ errorv.first('auto-form.'+item.model) }}</p>
            </div>
            <cell v-if="item.type === 'pageSelecter'" :key="index" :is-link="!item.disabled" @click.native="onPageSelecterClick(item)">
              <div class="content" slot="title">
                <p style="display: flex;">
                  <label style="flex: 0 0 auto; margin-right: 5px;" class="label" :class="{require: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model">{{item.label}}</label>
                  <input hidden v-model="item.value" v-validate="item.validate" :class="{'input': true, 'is-danger': errorv.has('auto-form.' + item.model) }" :name="item.model" type="text" :placeholder="item.placeholder || ('请选择' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))">
                  <span :style="item.valueDesc? 'color: black;': 'color: gray;' + 'flex: 1 1 100%; background: transparent;'">{{item.valueDesc || (item.placeholder || ('请选择' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length)))}}</span>
                  <!-- <input style="flex: 1 1 100%; background: transparent;" class="input-disabled" disabled :placeholder="item.placeholder || ('请选择' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" v-model="item.valueDesc"></input> -->
                </p>
                <p v-show="errorv.has('auto-form.'+item.model)" class="help is-danger">{{ errorv.first('auto-form.'+item.model) }}</p>
              </div>
            </cell>
            <div style="padding: 5px 0;" v-else-if="item.type === 'textInput'" class="content" :key="index">
              <p style="margin: 0; ">
                <label style="margin-bottom: 5px; padding: 5px 15px; line-height: 1.3em;" :class="{require: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model">{{item.label}}</label>
                <x-textarea data-vv-scope="auto-form" :readonly="item.disabled" style="margin-top: 5px; min-height: 20px;" class="text-input" :name="item.model" v-model="item.value" v-validate="item.validate" :placeholder="item.placeholder || ('请输入' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" autosize></x-textarea>
              </p>
              <p style="margin-left: 15px;" v-show="errorv.has('auto-form.'+item.model)" class="help is-danger">{{ errorv.first('auto-form.'+item.model) }}</p>
            </div>
            <div class="content textInputField" v-else-if="item.type === 'textInputField'" :key="index">
              <p style="margin: 0; display: flex;">
                <label style="padding: 10px 0 10px 15px; flex: 0 0 auto;" :class="{require: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model">{{item.label}}</label>
                <x-textarea data-vv-scope="auto-form" ref="textInputField" :readonly="item.disabled" style="padding: 10px 6px; flex: 1 1 100%; word-break: break-all;" :rows="1" class="text-input" :name="item.model" v-model="item.value" v-validate="item.validate" :placeholder="item.placeholder || ('请输入' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" autosize></x-textarea>
              </p>
              <p style="margin-left: 15px;" v-show="errorv.has('auto-form.'+item.model)" class="help is-danger">{{ errorv.first('auto-form.'+item.model) }}</p>
            </div>
            <cell style="height: 0; padding: 0;" v-else-if="item.type === 'uploadFile'" :key="index">
              <div class="content" style="position: absolute; right: 10px; top: 5px; z-index: 2; border: solid 2px white;">
                <div class="portrait">
                  <vue-core-image-upload data-vv-scope="auto-form" v-validate="item.validate" v-model="item.value" inputAccept="image/*" :key="count" :isXhr="false" :crop-btn="{'ok': '确定', 'cancel': '取消'}" crop-ratio="413:626" :name="item.model" crop="local" :data="item.data" :max-file-size="5242880" @imagechanged="onImageChanged($event, item)">
                    <img class="user-icon" :src="item.value? _imageUrlWithPath(item.value): logoSrc" />
                  </vue-core-image-upload>
                </div>
                <p v-show="errorv.has('auto-form.'+item.model)" class="help is-danger">{{ errorv.first('auto-form.'+item.model) }}</p>
              </div>
            </cell>
            <div v-else-if="item.type === 'selectDate' && !item.hidden" :key="index" class="content">
              <p style="margin: 0; position: relative;">
                <label class="label" :class="{require2: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model"></label>
                <datetime data-vv-scope="auto-form" :min-year="1950" :format="item.format" :class="'needsclick datetime '+(item.disabled?'listReadonly':'listEditable')+(item.value?'':' place-holder')" :readonly="item.disabled" :placeholder="item.placeholder || ('请选择' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" :title="item.label" :name="item.model" v-validate="item.validate" :options="item.options" v-model="item.value" @on-change="onDateChange(item)"></datetime>
                <span v-if="item.value" @click="item.value = ''" class="close-icon">
                  <i>×</i>
                </span>
              </p>
              <p style="margin-left: 15px; margin-bottom: 5px;" v-show="errorv.has('auto-form.'+item.model)" class="help is-danger">{{ errorv.first('auto-form.'+item.model) }}</p>
            </div>
            <cell v-else-if="item.type === 'mobileValidate'" :key="index">
              <div class="content" slot="title">
                <p class="mobile-validate">
                  <label class="label" :class="{require: item.validate&&item.validate.indexOf('required') !== -1}" :for="item.model">{{item.label}}</label>
                  <input :disabled="item.disabled" v-model="item.value" v-validate="item.validate" :class="{'input': true, 'is-danger': errorv.has('auto-form.' + item.model) }" :name="item.model" type="text" :placeholder="item.placeholder || ('请输入' + item.label.substring(0, item.label.lastIndexOf(':') !== -1? item.label.lastIndexOf(':'): item.label.length))" @change="onInputChange(item)">
                  <x-button action-type="button" type="primary" mini :disabled="item.shutDownTime !== 0 || !item.fetchEnable" @click.native="onMobileValidateFetch(item)">{{item.shutDownTime===0?'获取验证码':(item.shutDownTime+'秒')}}</x-button>
                </p>
                <p v-show="errorv.has('auto-form.'+item.model)" class="help is-danger">{{ errorv.first('auto-form.'+item.model) }}</p>
              </div>
            </cell>
            <cell-box v-else-if="item.component === 'inputField'" :key="index">
              <div class="new-content">
                <p style="margin: 0;">
                  <input-field data-vv-scope="auto-form" :type="item.type" :label="item.label" :name="item.model" data-vv-value-path="text" v-model="item.value" v-validate="item.validate" :validate="item.validate" :placeholder="item.placeholder"></input-field>
                </p>
                <p v-show="errorv.has('auto-form.'+item.model)" class="danger">{{ errorv.first('auto-form.'+item.model) }}</p>
              </div>
            </cell-box>
          </template>
        </group>
        <template v-else>
          <div class="submit" v-if="items.type === 'submit'" :key="index">
            <button class="btn-primary" type="button" action-type="button" @click="onSubmit">{{items.title? items.title: '提交'}}</button>
          </div>
          <div class="editForm" v-if="items.type === 'editForm'" :key="index">
            <button v-for="(item, index) in items.buttons" :key="index" :class="item.type === 'primary'? 'btn-primary': 'btn-normal'" @click="onButtonAction(item.action)" type="button">{{item.name}}</button>
          </div>
        </template>
      </template>
    </form>
  </div>
</template>

<script>
import Vue from 'vue'
import { Group, Cell, CellBox, Selector, XTextarea, Checker, CheckerItem, XButton, Datetime, Flexbox, FlexboxItem } from 'vux'
import { ToastPlugin } from 'vux'
Vue.use(ToastPlugin)
import { is } from './js/utils'
import VueCoreImageUpload from 'vue-core-image-upload'
import config from './js/config'
import { _mapForm } from './js/helper'
import logoSrc from './person.jpeg'

import InputField from './components/inputField'
import VeeValidate from './js/veeValidate'

const _config = {
  errorBagName: 'errorv', // change if property conflicts.
  fieldsBagName: 'fields',
  delay: 0,
  locale: 'zh_CN',
  dictionary: null,
  strict: true,
  enableAutoClasses: true,
  classNames: {
    touched: 'touched', // the control has been blurred
    untouched: 'untouched', // the control hasn't been blurred
    valid: 'valid', // model is valid
    invalid: 'invalid', // model is invalid
    pristine: 'pristine', // control has not been interacted with
    dirty: 'dirty' // control has been interacted with
  },
  events: 'input|blur',
  inject: true
}

Vue.use(VeeValidate, _config)

export default {
  components: {
    Group, Cell, CellBox, Selector, XTextarea, Checker, CheckerItem, XButton, Datetime, VueCoreImageUpload, Flexbox, FlexboxItem, InputField
  },
  // lifecycle
  mounted: function () {
    this.data = this.formItems
    const obj = {}
    for (let index = 0, arr = []; index < this.formItems.length; index++) {
      arr = this.formItems[index]
      for (let i = 0; i < arr.length; i++) {
        let item = arr[i]
        if (item.type === 'mobileValidate' && item.fetchPassTime !== 0) { // 检查设置倒计时组件
          this.onMobileValidateFetch(item, item.fetchPassTime)
        }
      }
    }
    this.manaValidator = obj
  },

  name: 'page-formRender',
  props: ['formItems', 'submitTitle'],
  data: function () {
    return {
      form: {

      },
      count: 1,
      data: [],
      coreImageData: {},
      manaValidator: {},
      logoSrc: logoSrc
    }
  },
  computed: {
    forms: function () {
      if (!this._forms && this.data.length) {
        this._forms = _mapForm(this.data)
      }
      return this._forms
    }
  },
  methods: {
    _imageUrlWithPath: function (path) {
      if (/^http/.test(path)) {
        return path
      } else if (/^data:image\/(jpeg|png|gif);base64,/.test(path)) {
        return path
      } else if (path) {
        return config.url.base + path
      } else {
        return ''
      }
    },
    onSubmit: function (e) {
      this.validateForm((formData) => {
        this.$emit('handleSubmit', formData)
      })
    },
    onButtonAction: function (action) { // 新增和编辑项目表单中的按钮，应该使用solt优化
      switch (action) {
        case 'cancel':
          this.$emit('handleEditForm', { action })
          break
        default:
          this.validateForm((formData) => {
            this.$emit('handleEditForm', { action, formData })
          })
          break
      }
    },
    onSelectorChange: function (item) {
      this.$emit('handleSelectorChanged', item)
    },
    onDateChange: function (item) {
      this.$emit('handleSelectorChanged', item)
    },
    onPageSelecterClick: function (e) {
      if (!e.disabled) {
        this.$emit('handlePageSelecter', e)
      }
    },
    onPasswordInputClick: function (item) {
      item.showPassword = !item.showPassword
    },
    onMobileValidateFetch: function (item, time) {
      const nowTime = time || new Date().getTime()
      item.fetchPassTime = nowTime
      const shutDown = function (time, after) {
        const nowTime = new Date().getTime()
        const sec = after - Math.floor((nowTime - time) / 1000)
        if (sec <= 0) {
          item.shutDownTime = 0
        } else {
          item.shutDownTime = sec
          setTimeout(() => {
            shutDown(time, after)
          }, 1000)
        }
      }
      shutDown(nowTime, 60)
      if (!time) {
        this.$emit('handleMobileValidateFetch')
      }
    },
    isArray: function (obj) {
      return is.Array(obj)
    },
    clearValidate: function () {
      this.$validator.reset()
    },
    onInputChange: function (item) {
      this.$emit('onInputChange', item)
    },
    validateForm: function (block) {
      const keys = Object.keys(this.manaValidator)
      const dic = {}
      for (var i = 0; i < keys.length; i++) {
        var item = keys[i]
        dic[item] = this.manaValidator[item].value
      }
      this.$validator.validateAll().then(result => {
        if (!result) {
          // validation failed.
          const msgs = this.makeErrorMessage(validator.errors)
          if (msgs.length) {
            let msg = msgs[0]
            this.$vux.toast.text(`${msg[0]}：${msg[1]}`)
            // this.$store.commit('showToast', { type: 'text', text: `${msg[0]}：${msg[1]}`, loading: true })
          } else {
            this.$vux.toast.text('填写有误')
            // this.$store.commit('showToast', { type: 'text', text: '填写有误', loading: true })
          }
        } else {
          this.$validator.validateAll('auto-form').then((result) => {
            if (!result) {
              const msgs = this.makeErrorMessage(this.$validator.errors)
              if (msgs.length) {
                let msg = msgs[0]
                this.$vux.toast.text(`${msg[0]}：${msg[1]}`)
              } else {
                this.$vux.toast.text('填写有误')
              }
            } else {
              const formData = {}
              for (let index = 0; index < this.data.length; index++) {
                let obj = this.data[index]
                if (is.Array(obj)) {
                  for (let i = 0; i < obj.length; i++) {
                    let item = obj[i]
                    item.model && !item.hidden && (formData[item.model] = item.value)
                  }
                } else {
                  obj.model && !item.hidden && (formData[obj.model] = obj.value)
                }
              }
              if (block) {
                block(formData)
              }
            }
            // success stuff.
          }).catch(() => {
            // something went wrong (non-validation related).
          })
        }
      }).catch(() => {

      })
    },
    makeErrorMessage: function (errorBag) {
      // 通过errorbag和this.data 获取到某些字段的错误信息
      const errors = errorBag.items
      const messages = []
      if (errors.length) {
        for (let i = 0; i < errors.length; i++) {
          let error = errors[i]
          let message = []
          message[0] = this.forms[error.field].label
          message[1] = error.msg
          messages.push(message)
        }
        return messages
      }
      return []
    },
    onImageChanged: function (data, item) {
      let image = new Image()
      image.src = data
      image.onload = () => {
        let canvas = document.createElement('canvas')
        canvas.setAttribute('width', 413)
        canvas.setAttribute('height', 626)
        let ctx = canvas.getContext('2d')
        ctx.drawImage(image, 0, 0, 413, 626)
        item.value = canvas.toDataURL('image/jpeg', 0.5)
        this.count++
        this.$emit('onImageChanged', data)
      }
    }
  }
}
</script>

<style lang="less">
.page-formRender {
  .g-core-image-corp-container {
    z-index: 9999 !important;
    top: 44px !important;
    .image-aside {
      top: 50px !important;
    }
  }
  .g-core-image-upload-btn {
    font-size: 0;
    line-height: 0;
  }
  .g-core-image-upload-form {
    width: auto !important;
    height: 100% !important;
  }
  text-align: left;
  .input {
  }
  .is-danger {
    border-color: red;
  }
  .user-icon {
    display: block;
    pointer-events: none;
    height: 125px;
    width: 83px;
    background-color: lightgray;
  }
  .user-i {
    display: block;
    pointer-events: none;
    height: 98px;
    width: 70px;
    font-size: 64px;
    line-height: 98px;
    text-align: center;
    color: #c0ccda;
    background-color: #eff2f7;
  }
  .weui-cell {
    padding: 5px 15px;
  }
  .field {
    width: 100%;
    .input {
      display: flex;
      label {
        flex: 0 0 auto;
        margin-right: 6px;
      }
      input {
        flex: 1 1 100%;
      }
    }
  }
  .textInputField::before {
    content: " ";
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    height: 1px;
    border-top: 1px solid #d9d9d9;
    border-top-width: 1px;
    border-top-style: solid;
    border-top-color: rgb(217, 217, 217);
    color: #d9d9d9;
    -webkit-transform-origin: 0 0;
    transform-origin: 0 0;
    -webkit-transform: scaleY(0.5);
    transform: scaleY(0.5);
    left: 15px;
  }
  .textInputField {
    position: relative;
    padding: 5px 0;
    .vux-x-textarea::before {
      border-top: none;
    }
  }
  .content {
    font-size: 15px;
    line-height: 1.2em;
    .password-input {
      margin: 4px 0 5px !important;
      display: flex;
      align-items: center;
      label {
        flex: 0 0 auto;
        margin-right: 5px;
      }
      input {
        flex: 1 1 100%;
      }
      a {
        flex: 0 0 30px;
        height: 30px;
        line-height: 30px;
      }
    }
    .mobile-validate {
      margin: 4px 0 5px !important;
      display: flex;
      align-items: center;
      label {
        flex: 0 0 auto;
        margin-right: 5px;
      }
      input {
        flex: 1 1 100%;
      }
      button {
        flex: 0 0 auto;
        padding: 0 5px;
        min-width: 50px;
      }
    }
    input {
      border: none;
      border-bottom: none;
      outline: none;
      font-size: 15px;
    }
    input:disabled {
      color: #999 !important;
    }
    textarea:read-only {
      color: #999 !important;
    }
    .radio input {
      margin: 5px;
    }
    .require::before {
      content: "*";
      color: red;
      position: absolute;
      left: 5px;
    }
    .require2::before {
      content: "*";
      color: red;
      position: absolute;
      left: 5px;
      line-height: 50px;
    }
    .isrequire {
      content: "*";
      color: red;
    }
    > p:first-child {
      margin: 10px 0;
    }
    > p:last-child {
      line-height: 1em;
      font-size: 11px;
      color: red;
    }
    .portrait {
      margin: 0;
      box-shadow: 0 0 3px 1px #8492a6;
      .mask {
        background-color: rgba(0, 0, 0, 0.2);
      }
    }
    .text-input {
      line-height: 1.2em;
    }
    .selecter {
      display: flex;
      .weui-cell__hd {
        min-width: 60px;
        flex: 0 0 auto;
        margin-right: 6px;
        .weui-label {
          width: auto;
        }
      }
    }
    .datetime {
      display: flex;
      height: 40px;
      div:first-child {
        min-width: 60px;
        width: auto;
        color: #2c3e50;
      }
      .weui-cell__ft {
        text-align: left;
        min-width: 60px;
        margin-left: 6px;
        color: black;
      }
    }
    .place-holder {
      .weui-cell__ft {
        text-align: left;
        min-width: 60px;
        margin-left: 6px;
        color: gray;
      }
    }
    .listEditable {
      padding: 3px 15px;
    }
    .listReadonly {
      padding: 13px 15px;
      .weui-cell__ft {
        color: gray;
      }
    }
    .listPlaceHolder {
      .weui-cell__bd select {
        color: gray !important;
      }
    }
    .vux-selector-readonly {
      text-align: left;
    }
  }
  .vux-cell-box > div {
    width: 100%;
    padding: 0;
  }

  .submit {
    padding: 30px 20px;
    text-align: center;
    button {
      width: 80%;
      margin: auto;
    }
  }
  .editForm {
    display: flex;
    justify-content: space-between;
    padding: 5px;
    button {
      padding: 6px 20px;
    }
  }
  .btn-primary {
    font-size: 15px;
    color: white;
    background-color: #09bb07;
    border: solid 1px #09bb07;
    border-radius: 4px;
    padding: 8px 10px;
  }
  .new-content {
    .danger {
      font-size: 11px;
      line-height: 1em;
      color: red;
    }
  }
  .close-icon {
    height: 46px;
    width: 30px;
    position: absolute;
    display: flex;
    align-items: center;
    justify-content: center;
    i {
      font-size: 20px;
      color: white;
      width: 20px;
      height: 20px;
      background-color: #bbb;
      border-radius: 13px;
      text-align: center;
    }
    right: 30px;
    top: 0px;
  }
}
</style>
