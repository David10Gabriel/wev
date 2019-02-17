<template>
  <v-app id="app" :dark="dark">
    <v-navigation-drawer
      v-model="primaryDrawer.model"
      :permanent="primaryDrawer.type === 'permanent'"
      :temporary="primaryDrawer.type === 'temporary'"
      :clipped="primaryDrawer.clipped"
      :floating="primaryDrawer.floating"
      :mini-variant="primaryDrawer.mini"
      absolute
      overflow
      app
      disable-resize-watcher 
    ></v-navigation-drawer>
    <v-toolbar :clipped-left="primaryDrawer.clipped" app absolute>
      <!--v-toolbar-side-icon
        v-if="primaryDrawer.type !== 'permanent'"
        @click.stop="primaryDrawer.model = !primaryDrawer.model"
      ></v-toolbar-side-icon-->
      <!-- <v-spacer></v-spacer> -->
      <v-toolbar-title xs2>WhatsApp Export Viewer</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon xs2>
        <v-icon @click="dark = !dark">wb_incandescent</v-icon>
      </v-btn>
    </v-toolbar>
    <v-content>
      <v-container fluid>
        <v-layout align-center justify-center>
          <HomeScreen/>
          <!--v-flex xs10>
            <v-card>
              <v-card-text>
                <v-layout row wrap>
                  <v-flex xs12 md6>
                    <span>Scheme</span>
                    <v-switch v-model="dark" primary label="Dark"></v-switch>
                  </v-flex>
                  <v-flex xs12 md6>
                    <span>Drawer</span>
                    <v-radio-group v-model="primaryDrawer.type" column>
                      <v-radio
                        v-for="drawer in drawers"
                        :key="drawer"
                        :label="drawer"
                        :value="drawer.toLowerCase()"
                        primary
                      ></v-radio>
                    </v-radio-group>
                    <v-switch v-model="primaryDrawer.clipped" label="Clipped" primary></v-switch>
                    <v-switch v-model="primaryDrawer.floating" label="Floating" primary></v-switch>
                    <v-switch v-model="primaryDrawer.mini" label="Mini" primary></v-switch>
                  </v-flex>
                  <v-flex xs12 md6>
                    <span>Footer</span>
                    <v-switch v-model="footer.inset" label="Inset" primary></v-switch>
                  </v-flex>
                </v-layout>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn flat>Cancel</v-btn>
                <v-btn flat color="primary">Submit</v-btn>
              </v-card-actions>
            </v-card>
          </v-flex-->
        </v-layout>
      </v-container>
    </v-content>
    <v-footer :inset="footer.inset" app>
      <span class="px-2">&copy; WEV Solutions {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
  import HomeScreen from './components/HomeScreen'
  
  export default {
    name: 'App',
    components: {
      HomeScreen
    },
    data () {
      return {
        dark: false,
        drawers: ['Default (no property)', 'Permanent', 'Temporary'],
        primaryDrawer: {
          model: false,
          type: 'default (no property)',
          clipped: false,
          floating: false,
          mini: false
        },
        footer: {
          inset: false
        }
      }
    }
  }
</script>