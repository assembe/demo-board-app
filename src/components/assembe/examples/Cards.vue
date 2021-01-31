<template>
  <div>
    <v-skeleton-loader v-if="loading"
      class="mx-auto"
      type="list-item-two-line@3"
    ></v-skeleton-loader>
    <draggable v-else group="cards" :list="cards" :move="changeCard" :data-topic-id="topic.id">
      <v-list-item two-line
                   v-for="(card, i) in cards"
                   :key="i">
        <v-list-item-content>
          <v-list-item-title>{{ card.name }}</v-list-item-title>
          <v-list-item-subtitle>{{ card.description }}</v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>
    </draggable>
    <v-item-group>
      <v-text-field v-model="newCardName"
                    append-icon="mdi-plus"
                    @click:append="makeNewCard"
                    @keydown.enter="makeNewCard"
                    solo
      ></v-text-field>
    </v-item-group>
  </div>

</template>

<script>
  import sdk from "../../../api/sdk";
  import draggable from 'vuedraggable'

  export default {
    name: 'Card',
    components: {draggable},
    props:['topic'],
    data() {
      return {
        cards:[],
        newCardName:'',
        loading: true
      }
    },
    mounted() {
      this.getCards()
    },
    methods: {
      getCards() {
        this.loading = true;
        sdk.get('/api/topic/'+this.topic.id+'/cards?amount=10000&relations=0').then((response) => {
          this.cards = response.data.items;
          this.loading = false;
        })
      },
      makeNewCard() {
        sdk.post('/api/card', {
          name: this.newCardName,
          description:'',
          finished:false,
          topic: this.topic.id
        }).then((response) => {
          this.cards.push(response.data)
          this.newCardName = '';
        })
      },
      changeCard(event) {
        let card = event.draggedContext.element;
        let topicId = event.to.dataset.topicId;
        sdk.patch('/api/card/'+card.id, {
          topic: topicId
        })
      }
    }
  }
</script>

<style scoped>
  h1, h2 {
    font-weight: normal;
    text-align: center;
  }

</style>
