<template>
  <div id="CookieBanner" data-tone="optimized" v-if="showCookieModal">
    <div id="CookieBannerOverlay"></div>
    <div id="CookieBannerNotice" lang="pl" dir="ltr" role="dialog"
         aria-labelledby="CookieBannerTitle" aria-describedby="CookieBannerDescription" ng-non-bindable>
      <div class="cookiebanner__main">
        <div class="cookiebanner__main__inner">
          <div class="cookiebanner__main__content">
            <p id="CookieBannerTitle" role="status" class="cookiebanner__main__title">{{ content.title }}</p>
            <p id="CookieBannerDescription" class="cookiebanner__main__description">{{ content.details }}</p>
          </div>
          <div class="cookiebanner__buttons">
            <ul>
              <li>
                <button @click="declineAction()" class="cookiebanner__buttons__deny"
                        id="CybotCookiebotDialogBodyButtonDecline">{{ content.decline }}
                </button>
              </li>
              <li>
                <button @click="allowAllAction()" class="cookiebanner__buttons__accept"
                        id="CybotCookiebotDialogBodyLevelButtonLevelOptinAllowAll">{{ content.allowAll }}
                </button>
              </li>
              <li>
                <button class="cookiebanner__buttons__details" id="CustomCookiebotOpenDetails"
                        @click="toogleShowDetails()" aria-expanded="false"
                        aria-controls="CookieBannerDetails">{{
                    (showDetails ? content.hideDetails : content.personalize)
                  }}
                </button>
              </li>
            </ul>
          </div>
        </div>
      </div>

      <div v-if="showDetails" id="CookieBannerDetails" class="cookiebanner__details">
        <div class="cookiebanner__details__inner">
          <div class="cookiebanner__details__preferences">
            <fieldset class="cookiebanner__details__preferences__options">
              <legend class="screen-reader-text">[#LEVELOPTIN_ALLOW_SELECTION#]</legend>
              <CookieType v-model="approvals.necessary" inputValue="necessary" :disabled="true"
                          :content="content.types.a"/>
              <CookieType v-model="approvals.preferences" inputValue="preferences" :disabled="false"
                          :content="content.types.b"/>
              <CookieType v-model="approvals.statistics" inputValue="statistics" :disabled="false"
                          :content="content.types.c"/>
              <CookieType v-model="approvals.marketing" inputValue="marketing" :disabled="false"
                          :content="content.types.d"/>
            </fieldset>
            <div class="cookiebanner__details__preferences__buttons">
              <button v-on:click="submitChoosen(approvals)"
                      id="CybotCookiebotDialogBodyLevelButtonLevelOptinAllowallSelection"
                      class="cookiebanner__accept-selection">{{ content.allowSelected }}
              </button>
            </div>
          </div>
          <div class="cookiebanner__details__updated">
            <p v-if="lastUpdate">{{ content.lastUpdated }}: {{ lastUpdate }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CookieType from './CookieType.vue'

export default {
  name: 'VueCookiebotGTM',
  components: {
    CookieType
  },
  props: ['forceOpen'],
  mounted() {
    let storageCookies = window.localStorage.getItem("cookiegtm");
    if (storageCookies) {
      this.approvals = JSON.parse(storageCookies)
    }
    if (this.forceOpen || !storageCookies) {
      this.showCookieModal = true
    }
    this.sendGTM()
  },
  data() {
    return {
      lastUpdate: null,
      showCookieModal: true,
      showDetails: false,
      approvals: {
        necessary: true,
        preferences: false,
        statistics: false,
        marketing: false,
      },
      content: {
        title: 'Niniejsza strona korzysta z plików cookie',
        details: 'Wykorzystujemy pliki cookie do spersonalizowania treści i reklam, aby oferować funkcje społecznościowe i analizować ruch w naszej witrynie. Informacje o tym, jak korzystasz z naszej witryny, udostępniamy partnerom społecznościowym, reklamowym i analitycznym. Partnerzy mogą połączyć te informacje z innymi danymi otrzymanymi od Ciebie lub uzyskanymi podczas korzystania z ich usług.',
        allowAll: 'Zezwól na wszystkie',
        decline: 'Odmowa',
        personalize: 'Spersonalizuj',
        hideDetails: 'Ukryj detale',
        showDetails: 'Pokaż detale',
        allowSelected: 'Zezwól na wybrane',
        lastUpdated: 'Ostatnia aktualizacja',
        types: {
          a: {
            title: 'Niezbędne',
            description: 'Niezbędne pliki cookie przyczyniają się do użyteczności strony poprzez umożliwianie podstawowych funkcji takich jak nawigacja na stronie i dostęp do bezpiecznych obszarów strony internetowej. Strona internetowa nie może funkcjonować poprawnie bez tych ciasteczek.'
          },
          b: {
            title: 'Preferencje',
            description: 'Pliki cookie dotyczące preferencji umożliwiają stronie zapamiętanie informacji, które zmieniają wygląd lub funkcjonowanie strony, np. preferowany język lub region, w którym znajduje się użytkownik.'
          },
          c: {
            title: 'Statystyka',
            description: 'Statystyczne pliki cookie pomagają właścicielem stron internetowych zrozumieć, w jaki sposób różni użytkownicy zachowują się na stronie, gromadząc i zgłaszając anonimowe informacje.'
          },
          d: {
            title: 'Marketing',
            description: 'Marketingowe pliki cookie stosowane są w celu śledzenia użytkowników na stronach internetowych. Celem jest wyświetlanie reklam, które są istotne i interesujące dla poszczególnych użytkowników i tym samym bardziej cenne dla wydawców i reklamodawców strony trzeciej.'
          },
          e: {
            title: 'Nieklasyfikowane',
            description: 'Nieklasyfikowane pliki cookie, to pliki, które są w procesie klasyfikowania, wraz z dostawcami poszczególnych ciasteczek.'
          },
        }
      }
    }
  },
  methods: {
    sendGTM() {
      window.dataLayer = window.dataLayer || [];
      let approvals = this.approvals
      window.dataLayer.push({
        'event': 'Cookie Consent Init',
        consent: {
          approvals
        }
      });
    },
    submitAction(approvals) {
      window.localStorage.setItem("cookiegtm", JSON.stringify(approvals));
      this.showCookieModal = false
    },
    submitChoosen(approvals) {
      this.submitAction(approvals)
    },
    declineAction() {
      for (const [key] of Object.entries(this.approvals)) {
        this.approvals[key] = false
      }
      this.approvals.necessary = true
      this.submitAction(this.approvals)
    },
    allowAllAction() {
      for (const [key] of Object.entries(this.approvals)) {
        this.approvals[key] = true
      }
      this.submitAction(this.approvals)
    },
    toogleShowDetails() {
      this.showDetails = !this.showDetails
    }
  }
}
</script>

<style scoped>
:root {
  --cb-button-border: 0;
  --cb-button-border-radius: 0;
  --cb-button-background: #0f0f0f;
  --cb-button-font-weight: 500;
  --cb-button-font-size: 12px;
  --cb-button-font-family: sans-serif;
  --cb-title-font-family: serif;
  --cb-button-color: #fff;
  --cb-button-active-color: #0f0f0f;
  --cb-button-active-background: #ddd;
  --cb-button-active-border: 0;
}

#CookieBanner {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  position: fixed;
  z-index: 2147483645;
  min-height: 100vh;
  min-width: 100vw;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: 0 auto;
  pointer-events: none;
  display: flex;
  align-items: center;
  justify-content: center;
}


#CookieBanner, #CookieBanner * {
  box-sizing: border-box;
  text-underline-offset: .125em;
  outline-offset: 3px;
}

#CookieBannerOverlay {
  /* legacy browser compatibility */
  background: rgba(0, 0, 0, .4);
  /* legacy browser compatibility end */
  background: var(--cb-overlay-background, rgba(0, 0, 0, .4));
  width: 100%;
  height: 100%;
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  pointer-events: auto;
  z-index: 10;
  animation: cookieBannerFadeIn .25s ease-in-out;
  animation-fill-mode: forwards;
}


.is-closing-cookie-banner #CookieBannerOverlay {
  animation: cookieBannerFadeOut .25s ease-in-out;
  animation-fill-mode: forwards;
  pointer-events: none;
}

@media (prefers-reduced-motion: reduce) {
  #CookieBanner * {
    animation-duration: 0.001ms !important;
    transition-duration: 0.001ms !important;
  }
}

#CookieBannerNotice {
  /* legacy browser compatibility */
  color: #0f0f0f;
  max-width: 1080px;
  /* legacy browser compatibility end */
  padding: 40px 28px;
  overflow: auto;
  max-height: 100vh;
  max-height: calc(100vh - 24px);
  max-height: calc(100vh - 24px - env(safe-area-inset-bottom, 0));
  width: 100%;
  width: calc(100% - 24px);
  max-width: var(--cb-dialog-max-width, 1080px);
  background: #fff;
  border-radius: 10px;
  color: var(--cb-text-color, #0f0f0f);
  margin: 0 auto;
  z-index: 500;
  pointer-events: auto;
}

@media (min-width: 450px) {
  #CookieBannerNotice {
    padding: 24px;
    max-height: calc(100vh - 48px);
    max-height: calc(100vh - 48px - env(safe-area-inset-bottom, 0));
    width: calc(100% - 48px);
  }
}

.is-visible-cookie-banner #CookieBannerNotice {
  animation: cookieBannerSlideIn .25s ease-in-out;
  animation-fill-mode: forwards;
}

.is-closing-cookie-banner #CookieBannerNotice {
  animation: cookieBannerSlideOut .25s ease-in-out;
  animation-fill-mode: forwards;
  pointer-events: none;
}

@media (min-width: 800px) {
  #CookieBannerNotice {
    padding: 48px;
  }
}


@keyframes cookieBannerFadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@keyframes cookieBannerFadeOut {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

@keyframes cookieBannerSlideIn {
  0% {
    opacity: 0;
    transform: translateY(96px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes cookieBannerSlideOut {
  0% {
    opacity: 1;
    transform: translateY(0);
  }
  100% {
    opacity: 0;
    transform: translateY(96px);
  }
}

#CookieBanner button {
  background: none;
  border: 0;
  border-radius: 0;
  color: inherit;
  font: inherit;
  line-height: normal;
  overflow: visible;
  padding: 0;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
}

#CookieBanner button > * {
  pointer-events: none;
}

#CookieBanner ul, li {
  list-style: none;
  margin: 0;
  padding: 0;
  text-indent: 0;
}

#CookieBanner .screen-reader-text {
  border: 0;
  clip: rect(1px, 1px, 1px, 1px);
  clip-path: inset(50%);
  height: 1px;
  margin: -1px;
  overflow: hidden;
  padding: 0;
  position: absolute;
  width: 1px;
  word-wrap: normal !important;
}

/**
 * Content
 */

#CookieBanner .cookiebanner__main {
}

#CookieBanner .cookiebanner__main__inner {
  /* legacy browser compatibility */
  max-width: 1080px;
  /* legacy browser compatibility end */
  max-width: var(--cb-dialog-max-width, 1080px);
  margin: 0 auto;
}

#CookieBanner .cookiebanner__main__content {

}

@media (min-width: 800px) {
  #CookieBanner .cookiebanner__main__content {
    margin-right: 48px;
  }
}

#CookieBanner .cookiebanner__main__title {
  /* legacy browser compatibility */
  font-weight: bold;
  font-size: 20px;
  /* legacy browser compatibility end */
  font-family: var(--cb-title-font-family);
  font-weight: var(--cb-title-font-weight, bold);
  font-size: var(--cb-title-font-size-mobile, 20px);
  margin-top: 0;
  margin-bottom: 16px;
  text-align: center;
}

@media (min-width: 800px) {
  #CookieBanner .cookiebanner__main__title {
    /* legacy browser compatibility */
    font-size: 28px;
    /* legacy browser compatibility end */
    font-size: var(--cb-title-font-size-desktop, 28px);
    margin-bottom: 20px;
  }
}

#CookieBanner .cookiebanner__main__description {
  /* legacy browser compatibility */
  font-size: 15px;
  /* legacy browser compatibility end */
  font-family: var(--cb-description-font-family);
  font-weight: var(--cb-description-font-weight);
  font-size: var(--cb-description-font-size-mobile, 15px);
  line-height: 1.5;
  margin-top: 0;
  margin-bottom: 0;
  text-align: center;
}

@media (min-width: 800px) {
  #CookieBanner .cookiebanner__main__description {
    /* legacy browser compatibility */
    font-size: 16px;
    /* legacy browser compatibility end */
    font-size: var(--cb-description-font-size-desktop, 16px);
    line-height: 1.75;
  }
}

/**
 * Buttons
 */
#CookieBanner .cookiebanner__buttons {
  flex-shrink: 0;
  margin-top: 32px;
}

@media (min-width: 800px) {
  #CookieBanner .cookiebanner__buttons {
    margin-top: 40px;
  }
}


#CookieBanner .cookiebanner__buttons ul {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

#CookieBanner .cookiebanner__buttons li {
  margin-bottom: 12px;
  margin-left: 8px;
  margin-right: 8px;
  width: 100%;
}

@media (min-width: 800px) {
  #CookieBanner .cookiebanner__buttons li {
    width: auto;
  }
}

#CookieBanner .cookiebanner__buttons li:last-of-type {
  width: 100%;
  margin-bottom: 0;
}

#CookieBanner .cookiebanner__buttons__details {
  /* legacy browser compatibility */
  font-size: 16px;
  /* legacy browser compatibility end */
  font-size: var(--cb-details-button-font-size, 16px);
  margin-top: 8px;
  padding: 8px 16px;
  width: 100%;
  text-decoration: underline;
}


#CookieBanner .cookiebanner__buttons__details:hover,
#CookieBanner .cookiebanner__buttons__details:focus,
#CookieBanner .cookiebanner__buttons__details:active {
  text-decoration: underline;
}

#CookieBanner .cookiebanner__buttons__accept,
#CookieBanner .cookiebanner__buttons__deny {
  /* legacy browser compatibility */
  background: transparent;
  border-radius: 3px;
  font-weight: bold;
  font-size: 16px;
  border: 2px solid #0f0f0f;
  /* legacy browser compatibility end */
  transition: .2s ease-in-out;
  display: block;
  padding: 16px 24px;
  background: var(--cb-button-background, transparent);
  border-radius: var(--cb-button-border-radius, 3px);
  font-weight: var(--cb-button-font-weight, bold);
  font-size: var(--cb-button-font-size, 15px);
  font-family: var(--cb-button-font-family);
  border: var(--cb-button-border, 2px solid #0f0f0f);
  color: var(--cb-button-color, #0f0f0f);
  text-align: center;
  width: 100%;
}

@media (min-width: 800px) {
  #CookieBanner .cookiebanner__buttons__accept,
  #CookieBanner .cookiebanner__buttons__deny {
    font-size: var(--cb-button-font-size, 16px);
    padding: 18px 24px;
    min-width: 288px;
  }
}

#CookieBanner .cookiebanner__buttons__accept {
  color: var(--cb-accept-button-color, var(--cb-button-color, #fff));
  background: var(--cb-accept-button-background, var(--cb-button-background, #0f0f0f));
  border: var(--cb-accept-button-border, var(--cb-button-border, 2px solid #0f0f0f));
}

#CookieBanner .cookiebanner__buttons__accept:hover,
#CookieBanner .cookiebanner__buttons__accept:focus,
#CookieBanner .cookiebanner__buttons__accept:active {
  /* legacy browser compatibility */
  color: #fff;
  background: #0f0f0f;
  /* legacy browser compatibility end */
  color: var(--cb-accept-button-active-color, var(--cb-button-active-color, #fff));
  background: var(--cb-accept-button-active-background, var(--cb-button-active-background, #0f0f0f));
  border: var(--cb-accept-button-active-border, var(--cb-accept-button-border, var(--cb-button-active-border, var(--cb-button-border, 2px solid #0f0f0f))));
}


#CookieBanner .cookiebanner__buttons__deny:hover,
#CookieBanner .cookiebanner__buttons__deny:focus,
#CookieBanner .cookiebanner__buttons__deny:active {
  /* legacy browser compatibility */
  color: #fff;
  background: #0f0f0f;
  /* legacy browser compatibility end */
  color: var(--cb-button-active-color, #fff);
  background: var(--cb-button-active-background, #0f0f0f);
  border: var(--cb-button-active-border, var(--cb-button-border, 2px solid #0f0f0f));
}

/**
 * Details
 */

#CookieBanner .cookiebanner__details {
  margin-top: 24px;
  display: block;
}

#CookieBanner .cookiebanner__details__inner {
  /* legacy browser compatibility */
  max-width: 1080px;
  /* legacy browser compatibility end */
  max-width: var(--cb-dialog-max-width, 1080px);
  margin: 0 auto;
}

/**
 * Preferences
 */

#CookieBanner .cookiebanner__details__preferences {
  display: flex;
  flex-direction: column;
  margin-top: 16px;
}

@media (min-width: 800px) {
  #CookieBanner .cookiebanner__details__preferences {
    margin-top: 24px;
  }
}

#CookieBanner .cookiebanner__details__preferences__options {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  padding: 0;
  margin: 0;
  border: 0;
  min-width: 0;
}


#CookieBanner .cookiebanner__details__preferences__buttons {
  text-align: center;
  margin-top: 8px;
}

#CookieBanner .cookiebanner__accept-selection {
  /* legacy browser compatibility */
  background: transparent;
  border: 2px solid #0f0f0f;
  border-radius: 3px;
  font-size: 15px;
  font-weight: bold;
  /* legacy browser compatibility end */
  transition: .2s ease-in-out;
  border-radius: var(--cb-button-border-radius, 3px);
  background: var(--cb-button-background, transparent);
  font-weight: var(--cb-button-font-weight, bold);
  border: var(--cb-button-border, 2px solid #0f0f0f);
  font-size: var(--cb-button-font-size, 15px);
  font-family: var(--cb-button-font-family);
  color: var(--cb-button-color, #0f0f0f);
  display: block;
  padding: 16px 24px;
  text-align: center;
  width: 100%;
}

#CookieBanner .cookiebanner__accept-selection:hover,
#CookieBanner .cookiebanner__accept-selection:focus,
#CookieBanner .cookiebanner__accept-selection:active {
  /* legacy browser compatibility */
  color: #fff;
  background: #0f0f0f;
  /* legacy browser compatibility end */
  color: var(--cb-button-active-color, #fff);
  background: var(--cb-button-active-background, #0f0f0f);
  border: var(--cb-button-active-border, var(--cb-button-border, 2px solid #0f0f0f));
}

/**
 * Updated
 */

#CookieBanner .cookiebanner__details__updated {
  margin-top: 24px;
  text-align: center;
}

#CookieBanner .cookiebanner__details__updated p {
  font-size: 15px;
}
</style>
