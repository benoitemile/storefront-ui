<template>
  <div id="order-summary">
    <SfHeading
      title="Totals"
      :level="3"
      class="sf-heading--left sf-heading--no-underline title"
    />
    <div class="highlighted highlighted--total">
      <SfProperty
        name="Products"
        :value="totalItems"
        class="sf-property--full-width sf-property--large property"
      />
      <SfProperty
        name="Subtotal"
        :value="subtotal"
        class="sf-property--full-width sf-property--large property"
      />
      <SfProperty
        name="Shipping"
        :value="shippingMethod.price"
        class="sf-property--full-width sf-property--large property"
      />
      <SfDivider class="divider" />
      <SfProperty
        name="Total price"
        :value="total"
        class="sf-property--full-width sf-property--large property"
      />
    </div>
    <div class="highlighted promo-code">
      <SfInput
        v-model="promoCode"
        name="promoCode"
        label="Enter promo code"
        class="sf-input--filled promo-code__input"
      />
      <SfButton class="promo-code__button" @click="$emit('click:apply')"
        >Apply</SfButton
      >
    </div>

    <div class="characteristics">
      <SfCharacteristic
        v-for="characteristic in characteristics"
        :key="characteristic.title"
        :title="characteristic.title"
        :description="characteristic.description"
        :icon="characteristic.icon"
        size-icon="32px"
        color-icon="green-primary"
        class="characteristics__item"
      />
    </div>
    <div class="actions smartphone-only">
      <SfButton
        class="sf-button--full-width actions__button"
        @click="$emit('click:next')"
        >{{ buttonName }}</SfButton
      >
      <SfButton
        class="sf-button--text actions__button actions__button--secondary"
        @click="$emit('click:back')"
        >Go back</SfButton
      >
    </div>
  </div>
</template>
<script>
import {
  SfHeading,
  SfButton,
  SfDivider,
  SfProperty,
  SfCharacteristic,
  SfInput,
} from "@storefront-ui/vue";
export default {
  name: "OrderSummary",
  components: {
    SfHeading,
    SfButton,
    SfDivider,
    SfProperty,
    SfCharacteristic,
    SfInput,
  },
  props: {
    order: {
      type: Object,
      default: () => ({}),
    },
    shippingMethods: {
      type: Array,
      default: () => [],
    },
    paymentMethods: {
      type: Array,
      default: () => [],
    },
    characteristics: {
      type: Array,
      default: () => [],
    },
    buttonName: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      promoCode: "",
      showPromoCode: false,
      listIsHidden: false,
    };
  },
  computed: {
    products() {
      return this.order.products;
    },
    totalItems() {
      return (
        "" +
        this.products.reduce((previous, current) => {
          return previous + current.qty;
        }, 0)
      );
    },
    shipping() {
      return this.order.shipping;
    },
    shippingMethod() {
      const shippingMethod = this.shipping.shippingMethod;
      const method = this.shippingMethods.find(
        (method) => method.value === shippingMethod
      );
      return method ? method : { price: "$0.00" };
    },
    payment() {
      return this.order.payment;
    },
    paymentMethod() {
      const paymentMethod = this.payment.paymentMethod;
      const method = this.paymentMethods.find(
        (method) => method.value === paymentMethod
      );
      return method ? method : { label: "" };
    },
    subtotal() {
      const products = this.products;
      const subtotal = products.reduce((previous, current) => {
        const qty = current.qty;
        const price = current.price.special
          ? current.price.special
          : current.price.regular;
        const total = qty * parseFloat(price.replace("$", ""));
        return previous + total;
      }, 0);
      return "$" + subtotal.toFixed(2);
    },
    total() {
      const subtotal = parseFloat(this.subtotal.replace("$", ""));
      const shipping = parseFloat(this.shippingMethod.price.replace("$", ""));
      const total = subtotal + (isNaN(shipping) ? 0 : shipping);
      return "$" + total.toFixed(2);
    },
  },
};
</script>
<style lang="scss" scoped>
@import "~@storefront-ui/vue/styles";
.title {
  --heading-title-margin: 0 0 var(--spacer-xl) 0;
}
.property {
  margin: var(--spacer-base) 0;
}
.divider {
  --divider-border-color: var(--c-white);
  --divider-margin: calc(var(--spacer-base) * 2) 0 0 0;
}
.promo-code {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: var(--spacer-lg) 0 var(--spacer-base) 0;
  &__input {
    --input-background: var(--c-white);
    flex: 1;
  }
  &__button {
    --button-height: 30px;
  }
}
.characteristics {
  margin: 0 0 0 var(--spacer-xs);
  &__item {
    margin: var(--spacer-base) 0;
  }
}
.actions {
  background: var(--c-white);
  width: 100%;
  &__button {
    margin: var(--spacer-sm) 0;
  }
}
</style>
