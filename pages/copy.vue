<script setup lang="ts">
const englishCopy = ref("");

type Locale =  typeof LOCALES[number]['code'];
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
    
    return`{% if \${language} == 'en' %}${englishCopy.value}${translationText}{% endif %}`;
})
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
                    <span>Copy in {{ LOCALES.find((locale) => locale.code === key)?.name }} ({{ key }})</span>
                    <textarea v-model="translations[key as Locale]"  class="w-full border mb-4 p-4"></textarea>
                </div>
            </div>
        

            <span>Braze translation</span>
            <div class="p-12 bg-gray-200 font-mono">
                {{ translation }}
            </div>
        </div>
    </div>
</template>
