<template lang="pug">
  modal.coupon-details(v-on:closeModal="closeModal()")
    span(slot="title") {{ $i18n.t('coupon.couponDetails') }}
    div(slot="content")
      div.row
        div.col-xs.ta-right
          span.label {{ $i18n.t('coupon.offCode') }}
        div.col-xs.ta-left
          div.row.label-group.pull-left
            div.col-xs.text {{coupon.code}}
            div.icon.copy(@click="clipboardMessage()" v-clipboard="" v-bind:data-clipboard-text="coupon.code")

      div.row
        div.col-xs.ta-right
          span.label {{ $i18n.t('coupon.limit') }}
        div.col-xs.ta-left
          span.value.persian-num(v-if="coupon.limit") {{coupon.limit | numberFormat}}
          span.value(v-else) {{ $i18n.t('coupon.noLimit') }}

      div.row
        div.col-xs.ta-right
          span.label {{ $i18n.t('coupon.webservice') }}
        div.col-xs.ta-left
          span.value(v-if="!coupon.webservice_name") {{ $i18n.t('coupon.all') }}
          span.value(v-else) {{coupon.webservice_name}}

      div.row
        div.col-xs.ta-right
          span.label {{ $i18n.t('coupon.expirationDate') }}
        div.col-xs.ta-left
          span.value.persian-num {{coupon.expired_at | jalali('jYYYY-jMM-jDD')}}

      div.row
        div.col-xs.ta-right
          span.label {{ $i18n.t('coupon.minAmount') }}
        div.col-xs.ta-left
          span.value.persian-num {{coupon.min_amount | numberFormat}}

      div.row
        div.col-xs.ta-right
          span.label {{ $i18n.t('coupon.maxAmount') }}
        div.col-xs.ta-left
          span.value.persian-num {{coupon.discount.max_amount | numberFormat}}

      div.row
        div.col-xs.ta-right
          span.label {{ $i18n.t('coupon.offPercent') }}
        div.col-xs.ta-left
          span.value.persian-num(v-if="coupon.discount.percent") {{coupon.discount.percent}}
          span.value(v-else) -

</template>

<script>
  import modal from '../../partials/modal.vue';
  export default {
    name: 'pages-coupon-partials-details',
    data() {
      return {
        closeModalContent: true,
      }
    },
    props: ['coupon'],
    mounted(){
      this.closeModalContent = false
    },
    computed: {},
    methods: {
      closeModal() {
        this.$emit('closeModal');
        store.commit('clearValidationErrors');
      },
      clipboardMessage() {
        store.commit('flashMessage', {
          text: 'Copied',
          type: 'success',
          timeout: '500'
        });
      }
    },
    components: {
      modal
    }
  }

</script>
