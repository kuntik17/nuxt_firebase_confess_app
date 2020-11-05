<template>
  <div>
    <div justify="center" align="center" v-if="this.isLoading">
      <img src="~/assets/confess.gif" />
    </div>
    <div v-if="!this.isLoading">
      <v-row justify="center" align="center">
        <v-col cols="12" sm="8" md="6">
          <div class="d-flex justify-center">
            <v-text-field
              class="ma-10"
              label="Confess, confess, confess"
              hide-details="auto"
              v-model="newConfess"
            ></v-text-field>
          </div>
          <div class="d-flex justify-center mb-6">
            <v-btn @click="createConfess">Confess</v-btn>
          </div>
          <v-card>
            <v-list three-line>
              <v-subheader>Confess your sins</v-subheader>
              <template v-for="item in sortedArray(items)">
                <v-list-item :key="item.id">
                  <v-list-item-avatar>
                    <v-img :src="item.avatar"></v-img>
                  </v-list-item-avatar>

                  <v-list-item-content>
                    <v-list-item-title v-html="item.title"></v-list-item-title>
                    <v-list-item-subtitle
                      v-html="item.subtitle"
                    ></v-list-item-subtitle>
                  </v-list-item-content>
                  <v-list-item-icon @click="thumbUp(item)">
                    <v-badge :content="item.like" color="green" overlap>
                      <v-icon> mdi-thumb-up </v-icon>
                    </v-badge>
                  </v-list-item-icon>
                </v-list-item>
              </template>
            </v-list>
          </v-card>
        </v-col>
      </v-row>
    </div>
  </div>
</template>

<script>
import db from '../fb'

export default {
  data: () => ({
    items: [],
    newConfess: null,
    isLoading: false,
  }),
  async created() {
    const confessRef = db.collection('confess')
    const allConfess = await confessRef.get()

    allConfess.forEach((doc) => {
      const confess = {
        subtitle: doc.data().subtitle,
        like: doc.data().like,
        id: doc.id,
      }

      this.items.push(confess)
    })

    return true
  },
  methods: {
    async thumbUp(confess) {
      this.isLoading = true
      const ref = await db.collection('confess').doc(confess.id)

      const likes = confess.like + 1
      confess.like++

      // Set the 'capital' field of the city
      await ref.update({ like: likes })
      this.isLoading = false
    },
    sortedArray() {
      return this.items.slice().sort(function (a, b) {
        return b.like - a.like
      })
    },
    async createConfess() {
      this.isLoading = true
      const confess = {
        subtitle: this.newConfess,
        like: 0,
        id: '',
      }

      await db
        .collection('confess')
        .add(confess)
        .then((data) => {
          confess.id = data.id
          this.items.push(confess)
          this.isLoading = false
        })
    },
  },
}
</script>
