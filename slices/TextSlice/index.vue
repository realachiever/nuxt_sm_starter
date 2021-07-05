<template>
  <section>
    <div
      v-if="
        slice.slice_type === 'text_slice' &&
        slice.primary.textType === 'summary'
      "
      class="italic text-center py-4"
      v-html="htmlSerializer(slice.primary.text[0])"
    ></div>

    <div
      v-if="
        slice.slice_type === 'text_slice' &&
        slice.primary.textType === 'default'
      "
      class="mb-8"
      :class="{ 'flex jutify-between': slice.primary.image.desktop.url }"
    >
      <div
        v-if="slice.primary.image.desktop.url"
        class="w-[90px] h-[90px] flex-shrink-0 mr-[30px]"
      >
        <prismic-image :field="slice.primary.image" />
      </div>
      <div>
        <div
          v-for="(text, textIndex) in slice.primary.text.filter(
            (text) => text.type != 'o-list-item'
          )"
          :key="textIndex"
        >
          <h2
            v-if="text.type == 'heading2'"
            class="text-[2rem] mb-5 leading-[38px]"
            v-html="htmlSerializer(text)"
          ></h2>
          <h3
            v-if="text.type == 'heading3'"
            class="text-[1rem] font-bold mb-2 leading-[25px]"
            v-html="htmlSerializer(text)"
          ></h3>
          <p
            v-if="text.type == 'paragraph'"
            class="mb-4"
            v-html="htmlSerializer(text)"
          ></p>
        </div>
        <ol class="mt-4 pl-10 text-[18px] leading-[27px] list-decimal">
          <li
            v-for="(text, textIndex) in slice.primary.text.filter(
              (text) => text.type == 'o-list-item'
            )"
            :key="textIndex"
            v-html="htmlSerializer(text)"
          ></li>
        </ol>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    slice: {
      type: Object,
      required: true,
      default() {
        return {}
      },
    },
  },
  methods: {
    htmlSerializer(text) {
      let result = text.text
      if (text.spans.length > 0) {
        text.spans.forEach((span) => {
          const substring = text.text.substring(span.start, span.end)
          switch (span.type) {
            case 'strong': {
              result = result.replace(
                substring,
                `<strong>${substring}</strong>`
              )
              break
            }
            case 'hyperlink': {
              result = result.replace(
                substring,
                `<a href=${span.data.url} data-nuxt-link class="text-[#007bff] hover:text-[#0056b3] hover:underline">${substring}</a>`
              )
              break
            }
            default:
              break
          }
        })
      }

      return result
    },
  },
}
</script>
