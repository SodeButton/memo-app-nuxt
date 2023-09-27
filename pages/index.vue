<template>
  <v-container style="max-width: 600px" class="mt-2">
    <v-row>
      <v-col cols="6">
        <h2>メモアプリ</h2>
      </v-col>
      <v-col cols="6" class="text-right">
        <v-btn @click="createMemo()" >新しいメモ</v-btn>
      </v-col>
      <v-col cols="12">
        <v-text-field
          v-model="search"
          label="検索..."
          placeholder="検索..."
          single-line
          hide-details
          solo
          append-icon="mdi-magnify"
        ></v-text-field>
      </v-col>
      <v-col cols="12" class="text-center" v-if="isLoading">
        <v-progress-circular
          indeterminate
          size="50"
          width="5"
        ></v-progress-circular>
      </v-col>
      <v-col cols="12" class="text-center" v-if="memoData.length === 0 && !isLoading">
        <p class="grey--text ma-auto mt-10" style="max-width: 280px">上の新しいメモボタンをクリックしてメモを作成します</p>
      </v-col>
      <v-col v-for="(item, index) in memoData" :v-key="index" cols="12" v-if="memoData.length !== 0 && !isLoading">
        <v-hover v-slot="{ hover }">
          <v-card>
            <v-sheet
              max-width="100%"
              height="4px"
              color="yellow darken-1"
            ></v-sheet>
            <div v-if="!hover">
              <p class="v-overlay--absolute" style="top:10px; right:0;">{{item.lastUpdate}}</p>
            </div>
            <v-card-actions style="width: 100%; position:relative;">
              <v-menu :offset-y="true" left bottom :close-on-click="true">
                <template v-slot:activator="{on, attrs}">
                  <v-btn
                    :class="{'show-btns': hover}"
                    plain
                    :ripple="false"
                    v-bind="attrs"
                    v-on="on"
                    icon
                    absolute
                    top
                    right
                    style="top:0; right:0; color: rgba(255, 255, 255, 0);"
                  >
                    <v-icon>mdi-dots-horizontal</v-icon>
                  </v-btn>
                </template>
                <v-list class="align-center">
                  <v-list-item link :href="'/edit?id='+item.id">
                    <v-list-item-icon><v-icon>mdi-note-edit-outline</v-icon></v-list-item-icon>
                    <v-list-item-title>メモを開く</v-list-item-title>
                  </v-list-item>
                  <v-list-item link href="/" @click="deleteMemo(item.id)">
                    <v-list-item-icon><v-icon>mdi-trash-can-outline</v-icon></v-list-item-icon>
                    <v-list-item-title>メモの削除</v-list-item-title>
                  </v-list-item>
                </v-list>
              </v-menu>
            </v-card-actions>
            <v-card-text class="white--text">
              <div v-for="(text, index) in item.texts" v-if="index <= 10">
                <span v-if="index < 10">{{text}}<br></span>
                <span v-if="index === 10">...</span>
              </div>
            </v-card-text>
          </v-card>
        </v-hover>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

export default {
  name: 'IndexPage',
  data() {
    return {
      textData: [],
      memoData: [],
      isLoading: true,
      search: '',
    }
  },
  methods: {
    createMemo() {
      this.$router.push('/create');
    },
    deleteMemo(id) {
      const db = this.$fire.firestore;
      const memoCollection = db.collection("memoData");
      try {
        memoCollection.doc(id).delete();
        this.isLoading = true;
      } catch (e) {
        console.log(e);
      }
    },
  },
  created() {
    const db = this.$fire.firestore;
    const memoCollection = db.collection("memoData");

    try {
      memoCollection.orderBy("lastUpdate", "desc").onSnapshot((snapshot) => {
        const items = [];
        snapshot.forEach((doc) => {
          const texts = doc.data().text.split("\n");
          console.log(doc.data().lastUpdate.seconds * 1000);
          const date = doc.data().lastUpdate.toDate();
          const lastUpdate = date.toLocaleString();
          items.push({texts: texts, id: doc.id, lastUpdate: lastUpdate});
        });

        this.memoData = items;
        this.isLoading = false;
      });
    } catch (e) {
      console.log(e);
    }
  },
  watch: {
    memoData: function () {
      this.isLoading = false;
    }
  }
}
</script>

<style scoped>
.show-btns {
  color: rgba(255, 255, 255, 255) !important;
}
</style>
