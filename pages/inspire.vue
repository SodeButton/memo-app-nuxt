<template>
  <v-row>
    <v-col class="text-center">
      <img src="/v.png" alt="Vuetify.js" class="mb-5" />
      <blockquote class="blockquote">
        &#8220;First, solve the problem. Then, write the code.&#8221;
        <footer>
          <small>
            <em>&mdash;John Johnson</em>
            {{getTestData()}}
          </small>
        </footer>
      </blockquote>
    </v-col>
  </v-row>
</template>

<script>
let testText;
export default {
  name: 'InspirePage',
  data: {
  },
  methods: {
    async getTestData() {
      try {
        const res = await this.$fire.firestore.collection("testData").get();
        testText.value = res.docs.map(doc => {
          console.log(doc.data());
        })
      } catch (e) {
        console.log(e);
      }
    }
  }
}
</script>
