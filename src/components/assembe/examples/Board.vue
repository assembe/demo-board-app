<template>
  <div class="board-container">
    <v-card width="300" class="mr-2 mb-2 board-card"
            v-for="(topic, i) in topics"
            :key="i"
    >
      <v-card-title v-text="topic.name"></v-card-title>
      <v-container>
        <cards :topic="topic"></cards>
      </v-container>
    </v-card>
    <template>
      <div class="text-center">
        <v-dialog
          v-model="newTopicDialog"
          width="500"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="red lighten-2"
              dark
              v-bind="attrs"
              v-on="on"
            >
              <v-icon>mdi-plus</v-icon>
            </v-btn>
          </template>

          <v-card>
            <v-card-title class="headline">
              New Topic
            </v-card-title>

            <v-card-text>
              <v-text-field v-model="newTopicName" label="New Topic Name"></v-text-field>
            </v-card-text>

            <v-divider></v-divider>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="primary"
                text
                @click="makeNewTopic"
              >
                Create
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </div>
    </template>
  </div>
</template>

<script>
  import sdk from "../../../api/sdk";
  import draggable from 'vuedraggable'
  import Cards from "./Cards";

  export default {
    name: 'Board',
    components: {draggable, Cards},
    data() {
      return {
        topics:[],
        newTopicName:'',
        newTopicDialog:false,
      }
    },
    mounted() {
      this.getTopics()
    },
    methods: {
      getTopics() {
        sdk.get('/api/topic?amount=10000&relations=0').then((response) => {
          this.topics = response.data.items;
        })
      },
      makeNewTopic() {
        sdk.post('/api/topic', {
          name: this.newTopicName
        }).then((response) => {
          this.newTopicDialog = false;
          this.newTopicName = '';
          this.getTopics()
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

  .board-container {
    display: flex;
    flex-shrink: 0;
    flex-direction: row;
    overflow-x: auto;
    height: 100%;
  }

  .board-card{
    flex-shrink: 0;
    width: 300px;
  }

</style>
