<template>
    <div class="mb-12 text-center">
      <div class="flex md:flex-row flex-col">
        <div class="flex-1">
          <Qrcode
            content="12o6bgyC3sKr9dA1SQ6aUiXnChYewzdFzK"
            :title="t('onchain_title')"
            link="bitcoin:12o6bgyC3sKr9dA1SQ6aUiXnChYewzdFzK"
          />
        </div>
  
        <div class="flex-1">
          <Qrcode
            content="lightningnode@strike.me"
            :title="t('lightning_title')"
            link="lightning:lightningnode@strike.me"
          />
        </div>
      </div>
  
        {{ t('thank_you_message') }}
      
    </div>
  </template>
  
  <script setup>
  import { useI18n } from 'vue-i18n';
  
  const { t } = useI18n({ useScope: "local" });

  </script>
  
  <i18n lang="json">
{
  "da": {
    "onchain_title": "⛓️‍💥 Onchain",
    "lightning_title": "⚡️ Lightning",
    "thank_you_message": "Tak for din støtte!"
  },
  "de": {
    "onchain_title": "⛓️‍💥 Onchain",
    "lightning_title": "⚡️ Lightning",
    "thank_you_message": "Vielen Dank für Ihre Unterstützung!"
  },
  "en": {
    "onchain_title": "⛓️‍💥 Onchain",
    "lightning_title": "⚡️ Lightning",
    "thank_you_message": "Thank you for your support!"
  },
  "es": {
    "onchain_title": "⛓️‍💥 Onchain",
    "lightning_title": "⚡️ Lightning",
    "thank_you_message": "¡Gracias por tu apoyo!"
  },
  "fr": {
    "onchain_title": "⛓️‍💥 Onchain",
    "lightning_title": "⚡️ Lightning",
    "thank_you_message": "Merci pour votre soutien !"
  },
  "nl": {
    "onchain_title": "⛓️‍💥 Onchain",
    "lightning_title": "⚡️ Lightning",
    "thank_you_message": "Bedankt voor je steun!"
  },
  "pt": {
    "onchain_title": "⛓️‍💥 Onchain",
    "lightning_title": "⚡️ Lightning",
    "thank_you_message": "Obrigado pelo seu apoio!"
  }
}
      
    </i18n>
    