<script setup lang="ts">
const inputLink = ref("");

/**
 * Given a link, returns the translation string to be used in the Braze email template.
 */
const getBrazeTranslationForLink = (englishLink: string) => {
  const translation = LOCALES.map((locale, index) => {
    const ifStatement = index === 0 ? "if" : "elsif";

    return `{% ${ifStatement} \${language} == '${locale.code}' %}${englishLink.replace(
      ".com",
      `.com/${locale.code}`,
    )}`;
  });

  return `${translation.join("")}{% else %}${englishLink}{% endif %}`;
};

const translation = computed(() => getBrazeTranslationForLink(inputLink.value));
</script>

<template>
    <div class="max-w-4xl mx-auto">
        <span>Link</span>
        <input v-model="inputLink" placeholder="https://www.example.com/some-path" class="w-full border mb-12 p-4" />

        <span>Braze translation</span>
        <div class="p-12 bg-gray-200 font-mono">
            {{ translation }}
        </div>
    </div>
</template>