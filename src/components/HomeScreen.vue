<template>
  <v-container>
    <v-layout wrap>
      <v-flex xs12>
        <v-card id="welcomingCard">
          <v-card-title>
            Bem vindo ao WhatsApp Export Viewer
          </v-card-title>
          <v-card-subtitle>
            <div>Aqui você poderá visualizar suas mensagens de WhatsApp exportadas como num chat!</div>
          </v-card-subtitle>
          <v-card-text>
            <div>Vamos começar, primeiro você deve exportar a sua conversa no aplicativo do WhatsApp e depois clicar em selecionar conversa abaixo...</div>
          </v-card-text>
          <v-card-actions v-show="true">
            <v-btn color="success" @click="$refs.inputChat.click()">Selecionar conversa</v-btn>
            <input v-show="false" id="inputChat" ref="inputChat" type="file" accept=".txt">
            <v-spacer></v-spacer>
            <v-chip v-show="chatFile.name" label>{{ chatFile.name }}</v-chip>
            <v-spacer></v-spacer>
            <v-btn depressed accesskey="n" color="success" @click="importChatFile">
              Próximo passo
              <v-icon>mdi-chevron-right</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>

        <br>

        <v-card id="chatCard">
          <v-card-title primary-title>
            Essa é a sua conversa
          </v-card-title>
          <v-card-text>
            <v-flex v-for="message in chatRendered.loadedMessagesArray" :key="message.data" :text-right="message.position" class="pa-1">
              <v-tooltip top>
                <template v-slot:activator="{ on }">
                  <v-chip v-if="message.position" color="green" text-color="white" pill v-on="on">
                    {{ message.text }}<v-avatar right color="green darken-3">{{ message.sendersAvatar }}</v-avatar>
                  </v-chip>
                  <v-chip v-else color="green" text-color="white" pill v-on="on">
                    <v-avatar left color="green darken-3">{{ message.sendersAvatar }}</v-avatar>{{ message.text }}
                  </v-chip>
                </template>
                <span>{{message.sender + ' em ' + message.date }}</span>
              </v-tooltip>
            </v-flex>
          </v-card-text>
          <v-card-actions v-show="true">
            <v-spacer></v-spacer>
            <v-btn depressed accesskey="n" color="success">
              Próximo passo
              <v-icon>mdi-chevron-right</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
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
        
        let lastSender = { name: null, position: true}

        loadedRawMessagesArray.forEach(rawMessage => {
          let currentSender = rawMessage.includes(":") && rawMessage.includes("]") ? rawMessage.split(":")[2].split("]")[1].trimLeft() : "Whatsapp"
          
          loadedMessagesArray.push({
            originalData: rawMessage,
            date: rawMessage.split("]")[0].replace("[", "").trimLeft(),
            sender: currentSender,
            sendersAvatar: rawMessage.slice(22).split(': ')[0].slice(0,1)[0],
            text: rawMessage.split(":").slice(3, 10000).join(),
            position: lastSender.name === currentSender ? lastSender.position : !lastSender.position
          })
          
          lastSender = { name: currentSender, position: lastSender.nome === currentSender ? lastSender.position : !lastSender.position}
        })

        this.chatRendered.loadedMessagesArray = loadedMessagesArray 
      }

    }
  }
</script>

<style>

</style>
