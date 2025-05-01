<script setup lang="ts">
import { ref, computed, onMounted, reactive, watchEffect } from 'vue';

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

// Dynamic tag replacements
const tagReplacements = reactive<Record<string, string>>({});

// Store detected tags from all translations
const detectedTags = computed(() => {
    const tagRegex = /\[([^\]]+)\]/g;
    const uniqueTags = new Set<string>();

    // Check English copy
    let match;
    if (englishCopy.value) {
        const regex = new RegExp(tagRegex);
        while ((match = regex.exec(englishCopy.value)) !== null) {
            uniqueTags.add(match[1]);
        }
    }

    // Check all translations
    Object.values(translations.value).forEach(text => {
        if (!text) return;

        const regex = new RegExp(tagRegex);
        while ((match = regex.exec(text)) !== null) {
            uniqueTags.add(match[1]);
        }
    });

    return Array.from(uniqueTags).sort();
});

// Apply tag replacements to a string
const applyTagReplacements = (text: string) => {
    let result = text;
    Object.entries(tagReplacements).forEach(([tag, value]) => {
        // Replace all instances of [TAG] with the value
        const regex = new RegExp(`\\[${tag}\\]`, 'g');
        result = result.replace(regex, value);
    });
    return result;
};

const translation = computed(() => {
    // Only include languages that have non-empty translations
    const translationText = LOCALES
        .filter((locale) => translations.value[locale.code] && translations.value[locale.code].trim() !== "")
        .map(locale => {
            const ifStatement = "{% elsif";
            // Apply tag replacements to this translation
            const processedTranslation = applyTagReplacements(translations.value[locale.code]);
            return `${ifStatement} \${language} == '${locale.code}' %}${processedTranslation}`;
        }).join("");

    // Apply tag replacements to English copy as well
    const processedEnglishCopy = applyTagReplacements(englishCopy.value);
    return `{% if \${language} == 'en' %}${processedEnglishCopy}${translationText}{% else %}${processedEnglishCopy}{% endif %}`;
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

    // Check for tag replacements in query params (format: tag_tagname=value)
    Array.from(urlParams.entries()).forEach(([key, value]) => {
        if (key.startsWith('tag_')) {
            const tagName = key.substring(4); // Remove 'tag_' prefix
            tagReplacements[tagName] = value;
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
    <!-- Tag replacements section (only shown if tags are detected) -->
    <div v-if="detectedTags.length > 0" class="mb-6 p-4 border rounded bg-gray-50">
      <h3 class="text-lg font-medium mb-3">Dynamic Tag Replacements</h3>
      <p class="text-sm text-gray-600 mb-3">
        Enter values for the tags found in your translations. The same value will be used across all languages.
      </p>
      
      <!-- Tag value inputs -->
      <div class="space-y-3">
        <div v-for="tag in detectedTags" :key="tag" class="flex items-center">
          <div class="w-1/3 font-mono bg-gray-200 px-3 py-2 rounded mr-3 flex items-center">
            [{{ tag }}]
          </div>
          <div class="flex-1">
            <input 
              v-model="tagReplacements[tag]" 
              :placeholder="`Replace [${tag}] with...`" 
              class="w-full border rounded p-2"
            />
          </div>
        </div>
      </div>
    </div>
    
    <div class="grid grid-cols-3 gap-x-6">
      <div>
        <span>Copy in English</span>
        <textarea v-model="englishCopy" placeholder="Hello, welcome to [CLUB]!" class="w-full border mb-4 p-4"></textarea>
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