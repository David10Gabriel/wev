<template>
  <v-container>
    <v-layout wrap>
      <v-flex xs12>
        <v-card id="welcomingCard">
          <v-card-title primary-title>
            <div>
              <h3 class="headline mb-0">Bem vindo ao WhatsApp Export Viewer</h3>
              <div>Aqui você poderá visualizar suas mensagens de WhatsApp exportadas como num chat!</div>
            </div>
          </v-card-title>
          <v-card-text>
              <div>Vamos começar, primeiro você deve exportar a sua conversa no aplicativo do WhatsApp e depois clicar em selecionar conversa abaixo...</div>
          </v-card-text>
          <v-card-actions v-show="true">
            <v-btn color="success" @click="$refs.inputChat.click()">Selecionar conversa</v-btn>
            <input v-show="false" id="inputChat" ref="inputChat" type="file" accept=".txt" @change="importChatFile">
            <v-spacer></v-spacer>
            <v-chip v-show="chatFile.name" label>{{ chatFile.name }}</v-chip>
            <v-spacer></v-spacer>
            <v-btn depressed accesskey="n" color="success" @click="true">
              Próximo passo
              <v-icon>navigate_next</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>

        <br>

        <v-card id="chatCard">
          <v-card-title primary-title>
            <div>
              <h3 class="headline mb-0">Essa é a sua conversa</h3>
            </div>
          </v-card-title>
          <v-card-text>
            <v-flex grow v-for="message in chatRendered.loadedMessagesArray" :key="message.data">
              <v-tooltip top>
                <v-chip slot="activator" color="green" text-color="white">
                  <v-avatar color="green darken-4">{{ message.sendersAvatar }}</v-avatar>
                  {{ message.message }}
                </v-chip>
                <span>{{message.sender + ' em ' + message.date }}</span>
              </v-tooltip>
            </v-flex>
          </v-card-text>
          <v-card-actions v-show="true">
            <v-spacer></v-spacer>
            <v-btn depressed accesskey="n" color="success" @click="true">
              Próximo passo
              <v-icon>navigate_next</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
  const fs = require('fs');
  
  export default {
    data: () => ({
      chatFile: {
        name: 'Nenhum arquivo selecionado',
      },
      chatRendered: {
        messagesArray: [],
        loadedMessagesArray: []
      }
    }),
    methods: {
      importChatFile () {
        this.chatFile = document.querySelector('#inputChat').files[0]
        
        if (this.chatFile.type !== 'text/plain') {
          //Adicionar snackbar avisando que o arquivo é inválido
          this.chatFile = { name: 'Arquivo inválido selecionado' }
          return false
        }
        
        this.readChatFile(this.chatFile)
      },
      readChatFile (file) {
        if (!file) return false
      
        let reader = new FileReader();

        reader.onload = (event) => {
          this.chatFile.content = event.target.result
          this.renderChat(this.chatFile.content)
        };

        reader.readAsText(file)
      },
      renderChat (content) {
        if (!content) return false

        let rawMessagesArray = content.split('\n').reverse()
        let loadedRawMessagesArray = rawMessagesArray.slice(0,100).reverse()
        let loadedMessagesArray = []
        
        loadedRawMessagesArray.forEach(rawMessage => {
          if (rawMessage) {
            loadedMessagesArray.push({
              data: rawMessage,
              date: rawMessage.slice(1, 20),
              sender: rawMessage.slice(22).split(': ')[0],
              sendersAvatar: rawMessage.slice(22).split(': ')[0].slice(0,1)[0],
              message: rawMessage.slice(22).split(': ').slice(1)[0]
            })
          }
        })
        console.log(loadedMessagesArray)
        this.chatRendered.loadedMessagesArray = loadedMessagesArray 
      }

    }
  }
</script>

<style>

</style>
