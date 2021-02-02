<template>
  <div class="board-container">
    <v-sheet width="300" class="mr-2 mb-2 board-card"
            v-for="(topic, i) in topics"
            elevation="0"
            :key="i"
            :color="getColor(topic.color)"
    >
      <v-card-title v-text="topic.name" @dblclick="editTopic(topic)"
                    :class="{'white--text':getColorDark(topic.color)}"
      ></v-card-title>
      <v-container>
        <cards :topic="topic"
               :color="getColor(topic.color)"
               :dark="getColorDark(topic.color)"
        ></cards>
      </v-container>
    </v-sheet>
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
    <v-dialog
      v-model="editingTopic"
      :persistent="savingTopic"
      max-width="600px"
    >
      <v-card :loading="savingTopic">
        <v-card-title>
          <span class="headline">{{ editingTopicData.name }}</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field v-model="editingTopicData.name"
                              outlined
                              label="Name"
                              required
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <v-select v-model="editingTopicData.color"
                          :items="colors"
                          label="Color"
                          outlined
                ></v-select>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>

          <v-btn color="red darken-1" text @click="deleteTopic(editingTopicData.id)">
            Delete
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="editingTopic = false">
            Close
          </v-btn>
          <v-btn color="blue darken-1" text @click="saveEditingTopic">
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
  import Cards from "./Cards";

  export default {
    name: 'Board',
    components: {draggable, Cards},
    data() {
      return {
        topics:[],
        newTopicName:'',
        newTopicDialog:false,
        editingTopicData: {
          id:0,
          name:'',
          color:'white',
        },
        editingTopic:false,
        savingTopic:false,
        colors: [
          'white', 'green','red','blue',
        ]
      }
    },
    mounted() {
      this.getTopics()
    },
    methods: {
      getColor(color) {
        switch (color){
          case 'green': return '#35ca35';
          case 'red': return '#d22d2d';
          case 'blue': return '#3b3bdb';
        }

        return 'white';
      },
      getColorDark(color) {
        switch (color){
          case 'green':
          case 'red':
          case 'blue': return true;
        }

        return false;
      },
      getTopics() {
        sdk.get('/api/topic?amount=10000&relations=0').then((response) => {
          this.topics = response.data.items;
        })
      },
      makeNewTopic() {
        sdk.post('/api/topic', {
          name: this.newTopicName,
          color:'white'
        }).then((response) => {
          this.newTopicDialog = false;
          this.newTopicName = '';
          this.getTopics()
        })
      },
      editTopic(topic) {
        this.editingTopicData.id = topic.id;
        this.editingTopicData.name = topic.name;
        this.editingTopicData.color = topic.color ? topic.color : 'white';
        this.editingTopic = true;
      },
      saveEditingTopic() {
        this.savingTopic = true;
        sdk.patch('/api/topic/'+this.editingTopicData.id, {
          name: this.editingTopicData.name,
          color: this.editingTopicData.color
        }).then((response) => {
          this.getTopics();
          this.savingTopic = false;
          this.editingTopic = false;
        })
      },
      deleteTopic(id) {
        this.savingTopic = true;
        sdk.delete('/api/topic/'+id).then((response) => {
          this.getTopics();
          this.savingTopic = false;
          this.editingTopic = false;
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
    user-select: none;
  }
</style>
