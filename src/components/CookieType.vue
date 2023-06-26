<template>
  <div class="cookiebanner__preference">
    <div class="cookiebanner__preference__heading">
      <input type="checkbox" :id="inputValue" :value="inputValue" v-model="model" :disabled="disabled"/>
      <label class="cookiebanner__preference__label" :for="inputValue">
        <div class="cookiebanner__preference__ui" aria-hidden="true">
          <div class="cookiebanner__preference__ui__handle"></div>
        </div>
        <div class="cookiebanner__preference__text">
          <p :aria-label="content.title" class="cookiebanner__preference__title">
            {{ content.title }}</p>
        </div>
      </label>
      <a class="cookiebanner__preference__toggle" aria-expanded="false"
         aria-controls="CookieBannerMoreNecessary" @click="toogleShowDetails()">
        {{ ( showDetails ? buttonContent.hideDetails : buttonContent.showDetails ) }}</a>
    </div>
    <div v-if="showDetails" id="CookieBannerMoreNecessary" class="cookiebanner__preference__more">
      <p class="cookiebanner__preference__more__description">{{ content.description }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CookieType',
  props: ['content', 'value', 'inputValue' , 'disabled'],
  computed: {
    model: {
      get() {
        return this.value;
      },
      set(value) {
        this.$emit("input", value);
      },
    },
  },
  data() {
    return {
      showDetails: false,
      buttonContent:{
        showDetails: 'Pokaż detale',
        hideDetails: 'Ukryj detale',
      }
    }
  },
  methods: {
    toogleShowDetails() {
      this.showDetails = !this.showDetails
    }
  }
}
</script>

<style scoped>

.cookiebanner__preference__heading {
}

@media (min-width: 540px) {
  .cookiebanner__preference__heading {
    display: flex;
    align-items: center;
  }
}

.cookiebanner__preference {
  background: #f4f4f4;
  padding: 16px;
  width: 100%;
  margin-bottom: 12px;
}

@media (min-width: 540px) {
  .cookiebanner__preference {
    margin-bottom: 16px;
  }
}

@media (min-width: 800px) {
  .cookiebanner__preference {
    padding: 16px;
  }
}

.cookiebanner__preference:last-of-type {
  margin-bottom: 0;
}

.cookiebanner__preference input {
  position: absolute;
  opacity: 0;
  width: 0;
  height: 0;
  overflow: hidden;
}

.cookiebanner__preference__label {
  display: flex;
  align-items: center;
  flex-grow: 1;
  flex-direction: column;
}

@media (min-width: 540px) {
  .cookiebanner__preference__label {
    flex-direction: row;
  }
}

.cookiebanner__preference input:focus + .cookiebanner__preference__label,
.cookiebanner__preference__label:hover {
  text-decoration: underline;
  cursor: pointer;
}

.cookiebanner__preference__ui {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  width: 80px;
  height: 44px;
  background: #868e96;
  border-radius: 40px;
  transition: all .2s ease-in-out;
  cursor: pointer;
}

.cookiebanner__preference__text {
  display: block;
  margin-top: 12px;
}

.cookiebanner__preference__text p{
  color:#000;
}

@media (min-width: 540px) {
  .cookiebanner__preference__text {
    margin-left: 16px;
    margin-top: 0;
  }
}

.cookiebanner__preference__title {
  display: block;
  font-weight: bold;
  margin: 0;
  font-size: 16px;
}

@media (min-width: 800px) {
  .cookiebanner__preference__title {
    font-size: 18px;
  }
}

.cookiebanner__preference__toggle {
  flex-shrink: 0;
  margin-left: 32px;
}

@media (max-width: 539px) {
  .cookiebanner__preference__toggle {
    flex-shrink: 0;
    display: block;
    text-align: center;
    width: 100%;
    margin-top: 16px;
    margin-left: 0;
  }
}

.cookiebanner__preference__toggle:hover,
.cookiebanner__preference__toggle:focus,
.cookiebanner__preference__toggle:active {
  text-decoration: underline;
}

.cookiebanner__preference__more {
  display: block;
  margin-top: 24px;
}

.cookiebanner__preference__more__description {
  margin: 0;
  font-size: 16px;
  color: #0e0c0c;
  text-transform: none;
}

.cookiebanner__preference__more__description p{
  color: #0e0c0c;
}

.cookiebanner__preference__ui__handle {
  display: flex;
  margin-left: 8px;
  justify-content: center;
  align-items: center;
  width: 32px;
  height: 32px;
  background: #fff;
  border-radius: 50%;
  transition: all .2s ease-in-out;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.2);
}


.cookiebanner__preference input:checked + label .cookiebanner__preference__ui {
  /* legacy browser compatibility */
  background: #16a085;
  /* legacy browser compatibility end */
  background: #16a085;
}

.cookiebanner__preference input:checked + label .cookiebanner__preference__ui__handle {
  margin-left: 50%;
  background: #fff;
}


.cookiebanner__preference__toggle {
  flex-shrink: 0;
  margin-left: 32px;
}

@media (max-width: 539px) {
  .cookiebanner__preference__toggle {
    flex-shrink: 0;
    display: block;
    text-align: center;
    width: 100%;
    margin-top: 16px;
    margin-left: 0;
  }
}

.cookiebanner__preference__toggle:hover,
.cookiebanner__preference__toggle:focus,
.cookiebanner__preference__toggle:active {
  text-decoration: underline;
}

</style>
