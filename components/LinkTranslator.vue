<script setup lang="ts">
const LOCALES = [
  {
    code: "nl",
    name: "Nederlands",
  },
  {
    code: "fr",
    name: "Français",
  },
  {
    code: "de",
    name: "Deutsch",
  },
  {
    code: "it",
    name: "Italiano",
  },
  {
    code: "id",
    name: "Bahasa Indonesia",
  },
  {
    code: "es",
    name: "Español",
  },
  {
    code: "pt",
    name: "Português",
  },
  {
    code: "br",
    name: "Português do Brasil",
  },
  {
    code: "ja",
    name: "日本語",
  },
  {
    code: "zh",
    name: "简体中文",
  },
  {
    code: "tr",
    name: "Türkçe",
  },
  {
    code: "ko",
    name: "한국어",
  },
];

const inputLink = ref("");

/**
 * Given a link, returns the translation string to be used in the Braze email template.
 *
 * Example:
 * ```
 * {% if ${language} == 'nl' %}https://www.matchwornshirt.com/nl/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'fr' %}https://www.matchwornshirt.com/fr/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'de' %}https://www.matchwornshirt.com/de/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'es' %}https://www.matchwornshirt.com/es/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'pt' %}https://www.matchwornshirt.com/pt/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'it' %}https://www.matchwornshirt.com/it/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'tr' %}https://www.matchwornshirt.com/tr/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'ja' %}https://www.matchwornshirt.com/ja/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'zh' %}https://www.matchwornshirt.com/zh/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% elsif ${language} == 'ko' %}https://www.matchwornshirt.com/ko/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% else %}https://www.matchwornshirt.com/category/football?utm_source=transactional&utm_medium=menu-football-&utm_campaign=mws_email&utm_content=mwscomms{% endif %}
 * ```
 */
const getBrazeTranslationForLink = (englishLink: string) => {
  const translation = LOCALES.map((locale, index) => {
    const ifStatement = index === 0 ? "if" : "elsif";

    return `{% ${ifStatement} \${language} == '${locale.code}' %}${englishLink.replace(
      "https://www.matchwornshirt.com",
      `https://www.matchwornshirt.com/${locale.code}`,
    )}`;
  });

  return `${translation.join("")}{% else %}${englishLink}{% endif %}`;
};

const translation = computed(() => getBrazeTranslationForLink(inputLink.value));
</script>

<template>
    <div class="max-w-4xl mx-auto">
        <span>Link</span>
        <input v-model="inputLink" placeholder="https://www.matchwornshirt.com/some-path" class="w-full border mb-12 p-4" />

        <span>Braze translation</span>
        <div class="p-12 bg-gray-200 font-mono">
            {{ translation }}
        </div>
    </div>
</template>