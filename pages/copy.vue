<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';

const englishCopy = ref("");
type Locale = typeof LOCALES[number]['code'];
const translations = ref<Record<Locale, string>>({
    nl: "",
    es: "",
    fr: "",
    de: "",
    it: "",
    pt: "",
    ja: "",
    ko: "",
    zh: "",
    br: "",
    id: "",
    tr: "",
});

const translation = computed(() => {
    const translationText = LOCALES.filter((locale) => !!translations.value[locale.code]).map(locale => {
        const ifStatement = "{% elsif";
        return `${ifStatement} \${language} == '${locale.code}' %}${translations.value[locale.code]}`;
    }).join("");
    return `{% if \${language} == 'en' %}${englishCopy.value}${translationText}{% else %}${englishCopy.value}{% endif %}`;
});

// Function to read query parameters and populate the translation fields
const loadFromQueryParams = () => {
    const urlParams = new URLSearchParams(window.location.search);

    // Check for English copy in query params
    const enParam = urlParams.get('en');
    if (enParam) {
        englishCopy.value = enParam;
    }

    // Check for all other supported languages
    Object.keys(translations.value).forEach((langCode) => {
        const paramValue = urlParams.get(langCode);
        if (paramValue) {
            translations.value[langCode as Locale] = paramValue;
        }
    });
};

// Load translations from query parameters on component mount
onMounted(() => {
    loadFromQueryParams();
});
</script>
<template>
<div>
  <div class="max-w-4xl mx-auto">
    <div class="grid grid-cols-3 gap-x-6">
      <div>
        <span>Copy in English</span>
        <textarea v-model="englishCopy" placeholder="Hello, world!" class="w-full border mb-4 p-4"></textarea>
      </div>
      <div v-for="key in Object.keys(translations)" :key="key">
        <span>Copy in {{LOCALES.find((locale) => locale.code === key)?.name}} ({{ key }})</span>
        <textarea v-model="translations[key as Locale]" class="w-full border mb-4 p-4"></textarea>
      </div>
    </div>
    <span>Braze translation</span>
    <TranslationResult :resultText="translation" />
  </div>
</div>
</template>