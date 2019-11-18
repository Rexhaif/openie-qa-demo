<template>
  <v-app id="inspire">
    <v-content>
      <v-container
        class="fill-width"
        fluid
      >
        <v-row align="center" justify="center">
          <v-col
            cols="12"
            sm="8"
            md="4"
          >
            <v-card class="elevation-12">
                <v-toolbar
                    color="primary"
                    dark
                    flat
                >

                <v-toolbar-title>Answering your tricky questions...</v-toolbar-title>
                <div class="flex-grow-1"></div>
                </v-toolbar>
                <v-card-text> 
                <v-form>
                        <v-text-field
                        label="Who has developed google?..."
                        name="text"
                        type="text"
                        v-model="input_text"
                        >    
                        </v-text-field>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <div class="flex-grow-1"></div>
                    <v-btn v-on:click="fetch_data" text icon color="pink">
                        <v-icon>mdi-message-text</v-icon>
                    </v-btn>
					<div class="flex-grow-1"></div>
                </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
        </v-container>
        <v-container
            fluid
        >
        <v-row align="center" justify="center">
            <v-card
              class="mx-auto"
              max-width="300"
              tile
            >
              <v-list shaped>
                <v-subheader>Answers</v-subheader>
                <v-list-item-group v-model="item" color="primary">
                  <v-list-item
                    v-for="(item, i) in items"
                    :key="i"
                  >
                    <v-list-item-content>
                      <v-list-item-title>{{ item.subject }} {{ item.relation }} {{ item.object }}</v-list-item-title>
                      <v-list-item-subtitle v-text="item.prob.toFixed(4)"></v-list-item-subtitle>
                      <v-divider
                        :key="i"
                      ></v-divider>
                    </v-list-item-content>
                  </v-list-item>
                  
                </v-list-item-group>
              </v-list>
            </v-card>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
  import axios from "axios";
  export default {
    props: {
      source: String,
    },
    data: () => ({
      input_text: "",
      drawer: null,
      items: [
        {
          "subject": "huawei",
          "relation": "developed",
          "object": "google",
          "prob": 0.01231231,
          "status": 'ok',
        }
      ],
    }),
    methods: {
      fetch_data: function() {

        const vm = this;
        console.log(this.input_text);
        axios
          .post(
            "https://cors-anywhere.herokuapp.com/https://qa.gpu.computer/predict",
            { question: this.input_text}, 
            { "headers" : {"Content-Type":"application/json"}}
          )
          .then(function (response) {
            let idx = 0;
            if(response.data.length === 0) {
              while (vm.items.length !== 0) {
                vm.items.pop();
              }
              vm.$set(vm.items, 0, {
                'subject': "No",
                'relation': "answers",
                'object': 'found',
                'status': 'not-found'
              });
            }
            for(let res of response.data) {
                res['status'] = 'ok'
                vm.$set(vm.items, idx, res);
                idx += 1;
            }
          })
          .catch(function (error) {
            console.log(error);
            while (vm.items.length !== 0) {
                vm.items.pop();
            }
            vm.$set(vm.items, 0, {
              'subject': "An",
              'relation': "error",
              'object': 'occured',
              'status': 'error'
            });
          })
      }
    },
  }
</script>
