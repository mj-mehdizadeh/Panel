<template lang="pug">
  div.inner-content
    div.row.nav-page-header
      div.col-lg-6.col-md-6.col-sm-6.col-xs-6
        p.page-title {{ $i18n.t('coupon.editCoupon') }}
        p.page-description {{ $i18n.t('coupon.editCouponDescription') }}

    loading(v-if="loadingContent")

    form(v-else autocomplete="on" onsubmit="event.preventDefault();")
      div.row.section.nav-create-coupon( :class="{'inactive-step': loadingContent}" )
        div.row.box
          <!--Right box-->
          div.right-box.col-lg-6.col-md-6.col-sm-12.col-xs-12
            div.row
              div.col-lg-4.col-md-4.col-sm-12.col-xs-12
                span.label {{ $i18n.t('coupon.offCode') }}
              div.col-lg-8.col-md-8.col-sm-12.col-xs-12
                input(v-focus="" v-validate="'required'" :class="{'input-danger': errors.has('code')}" v-bind:data-vv-as="$i18n.t('coupon.offCode')" type="text"  id="code" v-model="code" name="code"  :placeholder="$i18n.t('coupon.sampleCode')" autofocus tabindex="1"  )
                div.ta-right(v-if="validation('code')")
                  span.text-danger {{ errors.first('code') }}

            div.row
              div.col-lg-4.col-md-4.col-sm-12.col-xs-12
                input(name="coupon-type" v-model="type" value="webservice" type="radio" id="rdoWebservice")
                label.dis-ib.m-t-5(for="rdoWebservice")
                  span
                  | {{ $i18n.t('coupon.webservice') }}
              div.col-lg-8.col-md-8.col-sm-12.col-xs-12
                selectbox.selectbox.col-lg-12.col-md-12.col-sm-12.col-xs-12.no-margin(v-on:select="selectedWebservice" v-if="webserviceId || type" v-bind:selected="webserviceId" v-bind:data="webserviceSelection" v-bind:class="{'disable' : type == 'easypay' }" :placeholder="$i18n.t('webservice.selectWebservice')"  tabindex="2")
                div.ta-right(v-if="validation('webservice_id')")
                  span.text-danger {{ errors.first('webservice_id') }}

            div.row
              div.col-lg-4.col-md-4.col-sm-12.col-xs-12
                input(name="coupon-type" v-model="type" value="easypay" type="radio" id="rdoEasypay")
                label.dis-ib.m-t-5(for="rdoEasypay")
                  span
                  | {{ $i18n.t('coupon.easypay') }}
              div.col-lg-8.col-md-8.col-sm-12.col-xs-12
                zarin-link.no-margin(v-on:select="selectedEasypay" v-if="easyPayId || type" v-bind:class="{'disable' : type == 'webservice', 'input-danger': errors.has('easypay_id')}" v-bind:selected="easyPayId" :placeholder="$i18n.t('easypay.selectEasypay')")
                div.ta-right(v-if="validation('easypay_id')")
                  span.text-danger {{ errors.first('easypay_id') }}

            div.row
              div.col-lg-4.col-md-4.col-sm-12.col-xs-12
                span.label {{ $i18n.t('coupon.expirationDate') }}
              div.col-lg-8.col-md-8.col-sm-12.col-xs-12
                date-picker.persian-num(v-model="expired_at" :min="today")
                div.ta-right(v-if="validation('expired_at')")
                  span.text-danger {{ errors.first('expired_at') }}

          <!--Left box-->
          div.right-box.col-lg-6.col-md-6.col-sm-12.col-xs-12
            div.row
              div.col-lg-4.col-md-4.col-sm-12.col-xs-12
                input(type="checkbox" v-model="visibleLimit" id= "chkLimit" @change="emptyLimitValue()" tabindex="5")
                label(for="chkLimit")
                  span
                  | {{ $i18n.t('coupon.limit') }}

              div.col-lg-8.col-md-8.col-sm-12.col-xs-12(v-bind:class="{'disable' : !visibleLimit}")
                input.ltr-input(v-validate="'required'"  v-bind:data-vv-as="$i18n.t('coupon.limit')" :class="{'input-danger':errors.has('limit')}" type="text" v-model="limit" :placeholder="$i18n.t('coupon.limitPlaceHolder')")
                div.ta-right(v-if="validation('limit')")
                  span.text-danger {{ errors.first('limit') }}

            div.row
              div.col-lg-4.col-md-4.col-sm-12.col-xs-12
                span.label {{ $i18n.t('coupon.minAmount') }}
              div.col-lg-8.col-md-8.col-sm-12.col-xs-12
                span.text-help {{ $i18n.t('coupon.minAmountDescription') }}
                vue-numeric.ltr-input(v-validate="{ rules: {required: true}}" v-bind:data-vv-as="$i18n.t('coupon.minAmount')" :class="{'input-danger': errors.has('min_amount')}" :currency="$i18n.t('webservice.toman')" separator="," v-model="min_amount" name="min_amount" id="min_amount" v-bind:value="min_amount" :placeholder="$i18n.t('coupon.minAmountPlaceHolder')"  tabindex="6")
                div.ta-right(v-if="validation('min_amount')")
                  span.text-danger {{ errors.first('min_amount') }}

            div.row
              div.col-lg-4.col-md-4.col-sm-12.col-xs-12
                span.label {{ $i18n.t('coupon.offPercent') }}
              div.col-lg-8.col-md-8.col-sm-12.col-xs-12
                input.ltr-input(v-validate="'required|numeric|min_value:1|max_value:99'" maxlength="3"  v-bind:data-vv-as="$i18n.t('coupon.offPercent')" :class="{'input-danger': errors.has('percent')}" type="text" v-model="percent" name="percent" id="percent" :placeholder="$i18n.t('coupon.offPercentPlaceHolder')"  tabindex="8")
                div.ta-right(v-if="validation('percent')")
                  span.text-danger {{ errors.first('percent') }}

            div.row
              div.col-lg-4.col-md-4.col-sm-12.col-xs-12
                span.label {{ $i18n.t('coupon.maxAmount') }}
              div.col-lg-8.col-md-8.col-sm-12.col-xs-12
                span.text-help {{ $i18n.t('coupon.maxAmountDescription') }}
                vue-numeric.ltr-input(v-validate="{ rules: {required: true}}" v-bind:data-vv-as="$i18n.t('coupon.maxAmount')" :class="{'input-danger': errors.has('max_amount')}" :currency="$i18n.t('webservice.toman')" separator="," v-model="max_amount" name="max_amount" id="max_amount" v-bind:value="max_amount"  :placeholder="$i18n.t('coupon.maxAmountPlaceHolder')"  tabindex="7")
                div.ta-right(v-if="validation('max_amount')")
                  span.text-danger {{ errors.first('max_amount') }}

            div.row.nav-button
              div.col-xs
                button.btn.success.pull-left(v-ripple="" @click="validateForm" tabindex="9") {{$i18n.t('coupon.editCoupon')}}
                  svg.material-spinner(v-if="loading" width="25px" height="25px" viewBox="0 0 66 66" xmlns="http://www.w3.org/2000/svg")
                    circle.path(fill="none" stroke-width="6" stroke-linecap="round" cx="33" cy="33" r="30")

</template>

<script>
  import VuePersianDatetimePicker from 'vue-persian-datetime-picker'
  import selectbox from '../../partials/selectbox.vue';
  import loading from '../../partials/loading.vue';
  import zarinLink from '../../partials/zarinlinks';
  import VueNumeric from 'vue-numeric';

  export default {
    name: 'pages-coupon-partials-edit',
    data() {
      return {
        today: moment().add(1, 'd').format('jYYYY/jMM/jDD'),
        loadingContent: true,
        loading: false,
        code: '',
        max_amount: '',
        min_amount: '',
        webservice_id: null,
        easypay_id: null,
        easypay_name: null,
        expired_at: moment().add(1, 'd').format('jYYYY/jMM/jDD'),
        limit: '',
        type: '',
        percent: '',
        visibleLimit: false,
      }
    },
    computed: {
      webserviceSelection() {
        if (this.$store.state.auth.user.webservices) {
          let webservices = this.$store.state.auth.user.webservices.filter(
              function(webservice) {
                return webservice.status;
              }).map(function(webservice) {
            return {
              'title': webservice.name,
              'value': webservice.entity_id
            }
          });

          let webserviceAll = {
            'title': 'همه',
            'value': 'all'
          };

          webservices.unshift(webserviceAll);
          return webservices;
        }
      },
      validationErrors() {
        return this.$store.state.alert.validationErrors;
      },
      webserviceId() {
        if (!this.webservice_id && !this.easypay_id &&
            this.type === 'webservice') {
          return 'all';
        }
        return this.webservice_id;
      },
      easyPayId() {
        if (!this.webservice_id && !this.easypay_id &&
            this.type === 'easypay') {
          return {entity_id: 'all', title: 'همه'};
        }

        return {entity_id: this.easypay_id, title: this.easypay_name};
      }
    },
    created(){
      store.commit('clearValidationErrors');
      /*** Get coupon data***/
      this.getCoupon();
    },
    methods: {
      validateForm() {
        this.$validator.validateAll({
          code: this.code,
          expired_at: this.expired_at,
          min_amount: this.min_amount,
          max_amount: this.max_amount,
          percent: this.percent
        }).then((result) => {
          if (result) {
            this.editCoupon();
          }
        });
      },
      emptyLimitValue() {
        this.limit = '';
      },
      selectedWebservice(entityId) {
        this.webservice_id = entityId;
        this.easypay_id = '';
      },
      selectedEasypay(entityId) {
        this.easypay_id = entityId;
        this.webservice_id = '';
      },
      getCoupon() {
        this.$store.state.http.requests['coupon.getShow'].get(
            {'coupon_id': this.$route.params.entity_id}).then(
            (response) => {
              this.code = response.data.data.code;
              this.max_amount = response.data.data.discount.max_amount;
              this.min_amount = response.data.data.min_amount;
              this.webservice_id = response.data.data.webservice_id;
              this.easypay_id = response.data.data.easypay_id;
              this.easypay_name = response.data.data.easypay_name;
              this.expired_at = moment(response.data.data.expired_at).
                  format('jYYYY-jMM-jDD');
              this.limit = response.data.data.limit;
              this.type = response.data.data.type;
              this.percent = response.data.data.discount.percent;

              if (response.data.data.limit) {
                this.visibleLimit = true;
              }

              this.loadingContent = false;
            }, () => {
              this.loadingContent = false;
            }
        );
      },
      editCoupon() {
        this.loading = true;

        let couponData = {
          code: this.code,
          discount: {
            max_amount: this.clearNumber(this.max_amount),
            percent: this.percent
          },
          webservice_id: this.webserviceId,
          easypay_id: this.easyPayId.entity_id,
          expired_at: moment(this.expired_at, 'jYYYY/jMM/jDD').
              format('YYYY-MM-DD'),
          limit: this.limit,
          min_amount: this.clearNumber(this.min_amount),
          type: this.type,
        };

        let params = {
          coupon_id: this.$route.params.entity_id
        };

        this.$store.state.http.requests['coupon.getShow'].update(params,
            couponData).then(
            () => {
              this.loading = false;
              this.$router.push({name: 'coupon.index'})
            },
            (response) => {
              this.loading = false;
              store.commit('setValidationErrors',
                  response.data.validation_errors);
              store.commit('flashMessage', {
                text: response.data.meta.error_type,
                type: 'danger'
              });
            }
        )
      }
    },
    components: {
      selectbox,
      loading,
      zarinLink,
      datePicker: VuePersianDatetimePicker,
      VueNumeric
    }
  }

</script>
