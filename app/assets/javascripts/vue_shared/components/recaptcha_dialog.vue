<script>
import PopupDialog from './popup_dialog.vue';

export default {
  name: 'recaptcha-dialog',

  props: {
    html: {
      type: String,
      required: false,
      default: '',
    },
  },

  data() {
    return {
      script: {},
      scriptSrc: 'https://www.google.com/recaptcha/api.js',
    };
  },

  components: {
    PopupDialog,
  },

  methods: {
    appendRecaptchaScript() {
      this.removeRecaptchaScript();

      const script = document.createElement('script');
      script.src = this.scriptSrc;
      script.classList.add('js-recaptcha-script');
      script.async = true;
      script.defer = true;

      this.script = script;

      document.body.appendChild(script);
    },

    removeRecaptchaScript() {
      if (this.script instanceof Element) this.script.remove();
    },

    close() {
      this.removeRecaptchaScript();
      this.$emit('close');
    },

    submit() {
      this.$el.querySelector('form').submit();
    },
  },

  watch: {
    html() {
      this.appendRecaptchaScript();
    },
  },

  mounted() {
    window.recaptchaDialogCallback = this.submit.bind(this);
  },
};
</script>

<template>
<popup-dialog
  kind="warning"
  class="recaptcha-dialog js-recaptcha-dialog"
  :hide-footer="true"
  :title="__('Please solve the reCAPTCHA')"
  @toggle="close"
>
  <div slot="body">
    <p>
      {{__('We want to be sure it is you, please confirm you are not a robot.')}}
    </p>
    <div
      ref="recaptcha"
      v-html="html"
    ></div>
  </div>
</popup-dialog>
</template>
