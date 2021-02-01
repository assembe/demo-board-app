<template>
  <div>
    <v-skeleton-loader v-if="loading"
                       class="mx-auto"
                       type="list-item-two-line@3"
    ></v-skeleton-loader>
    <template v-else>
      <draggable group="cards" :list="cards" :move="changeCard" :data-topic-id="topic.id">
        <v-slide-x-transition group :data-topic-id="topic.id">
          <v-list-item two-line
                       v-for="card in cards"
                       @dblclick="editCard(card)"
                       :key="card.id">
            <v-list-item-content :class="{'done':card.finished}">
              <v-list-item-title v-text="card.name"></v-list-item-title>
              <v-list-item-subtitle v-text="card.description"></v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-slide-x-transition>
      </draggable>
    </template>
    <v-item-group>
      <v-text-field v-model="newCardName"
                    :append-icon="addingNewCard ? '' : 'mdi-plus'"
                    :loading="addingNewCard"
                    @click:append="makeNewCard"
                    @keydown.enter="makeNewCard"
                    solo
      ></v-text-field>
    </v-item-group>
    <v-dialog
      v-model="editingCard"
      :persistent="savingCard"
      max-width="600px"
    >
      <v-card :loading="savingCard">
        <v-card-title>
          <span class="headline">{{ editingCardData.name }}</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field v-model="editingCardData.name"
                              outlined
                              label="Name"
                              required
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <v-textarea v-model="editingCardData.description"
                            outlined
                            label="Description"
                ></v-textarea>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <v-switch v-model="editingCardData.finished"
                          label="Is finished"
                ></v-switch>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>

          <v-btn color="red darken-1" text @click="deleteCard(editingCardData.id)">
            Delete
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="editingCard = false">
            Close
          </v-btn>
          <v-btn color="blue darken-1" text @click="saveEditingCard">
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>

</template>

<script>
import sdk from "../../../api/sdk";
import draggable from 'vuedraggable'

export default {
  name: 'Card',
  components: {draggable},
  props: ['topic'],
  data() {
    return {
      cards: [],
      newCardName: '',
      loading: true,
      editingCard: false,
      addingNewCard: false,
      editingCardData: {
        id: 0,
        name: '',
        description: '',
        finished: false,
      },
      savingCard: false
    }
  },
  mounted() {
    this.getCards()
  },
  methods: {
    getCards() {
      this.loading = true;
      sdk.get('/api/topic/' + this.topic.id + '/cards?amount=10000&relations=0').then((response) => {
        this.cards = response.data.items;
        this.loading = false;
      })
    },
    makeNewCard() {
      this.addingNewCard = true;
      sdk.post('/api/card', {
        name: this.newCardName,
        description: '',
        finished: false,
        topic: this.topic.id
      }).then((response) => {
        this.cards.push(response.data)
        this.newCardName = '';
        this.addingNewCard = false;
      })
    },
    changeCard(event) {
      let card = event.draggedContext.element;
      let topicId = event.to.dataset.topicId;
      if (card.topic == topicId) {
        return;
      }

      sdk.patch('/api/card/' + card.id, {
        topic: topicId
      })
      card.topic = topicId;
    },
    editCard(card) {
      this.editingCardData.id = card.id;
      this.editingCardData.name = card.name;
      this.editingCardData.description = card.description;
      this.editingCardData.finished = card.finished;
      this.editingCard = true;
    },
    saveEditingCard() {
      this.savingCard = true;
      sdk.put('/api/card/' + this.editingCardData.id, {
        name: this.editingCardData.name,
        description: this.editingCardData.description,
        finished: this.editingCardData.finished,
        topic: this.topic.id
      }).then((response) => {
        this.modifyCard(this.editingCardData.id, this.editingCardData);
        this.savingCard = false;
        this.editingCard = false;
      })
    },
    modifyCard(id, data) {
      for (let i = 0; i < this.cards.length; i++) {
        const card = this.cards[i];
        if (card.id === id) {
            card.name= data.name;
            card.description= data.description;
            card.finished= data.finished;

          return;
        }
      }
    },
    removeCard(id) {
      for (let i = 0; i < this.cards.length; i++) {
        const card = this.cards[i];
        if (card.id === id) {
          this.cards.splice(i,1);
          return;
        }
      }
    },
    deleteCard(id) {
      this.savingCard = true;
      sdk.delete('/api/card/' + id).then((response) => {
        this.removeCard(id);
        this.savingCard = false;
        this.editingCard = false;
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

.done {
  text-decoration: line-through;
}

</style>
