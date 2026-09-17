<template>
  <q-layout view="lHh Lpr lFf">
    <q-layout-header>
      <q-toolbar
        color="primary"
        :glossy="$q.theme === 'mat'"
        :inverted="$q.theme === 'ios'"
      >
        <q-btn
          flat
          dense
          round
          @click="leftDrawerOpen = !leftDrawerOpen"
          aria-label="Menu"
        >
          <q-icon name="menu" />
        </q-btn>

        <q-toolbar-title>
          Inventário
          <div slot="subtitle">v 1.0.6</div>
        </q-toolbar-title>
      </q-toolbar>
    </q-layout-header>

    <q-layout-drawer
      v-model="leftDrawerOpen"
      :content-class="$q.theme === 'mat' ? 'bg-grey-2' : null"
    >
      <q-list
        no-border
        link
        inset-delimiter
      >
        <q-list-header>Menu</q-list-header>
        <q-list-header></q-list-header>
        <q-item @click.native="saveInventoryData()">
          <q-item-side icon="cloud_upload" />
          <q-item-main label="Exportar" sublabel="Exportar dados" />
        </q-item>
        <q-list-header></q-list-header>
        <q-list-header></q-list-header>
        <q-item @click.native="clearInventoryData()">
          <q-item-side icon="delete" />
          <q-item-main label="Apagar" sublabel="Apagar dados" />
        </q-item>

      </q-list>
    </q-layout-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL } from 'quasar'
import { axios } from 'axios'

export default {
  name: 'MyLayout',
  data () {
    return {
      leftDrawerOpen: this.$q.platform.is.desktop,
      items: []
    }
  },
  mounted () {
    if (this.$q.localStorage.has('items')) {
      console.log('localStorage.has(\'items\') == true')
      try {
        // this.items = JSON.parse(localStorage.getItem('items'))
        this.items = JSON.parse(this.$q.localStorage.get.item('items'))
        console.log('this.items.length' + this.items.length)
      } catch (e) {
        this.$q.localStorage.remove('items')
      }
    }
    // this.$refs.batchId.focus()
  },
  methods: {
    openURL,
    axios,
    loadInventoryData () {
      if (this.$q.localStorage.has('items')) {
        console.log('localStorage.has(\'items\') == true')
        try {
          // this.items = JSON.parse(localStorage.getItem('items'))
          this.items = JSON.parse(this.$q.localStorage.get.item('items'))
          console.log('this.items.length' + this.items.length)
        } catch (e) {
          console.log(e)
        }
      }
      // this.$refs.batchId.focus()
    },
    clearInventoryData () {
      this.$q.dialog({
        title: 'Apagar dados',
        message: 'Confirma a ação de apagar todos os dados de inventário?',
        ok: 'SIM',
        cancel: 'CANCELAR',
        preventClose: true
      }).then(() => {
        console.log('clearInventoryData: OK - clear all')
        if (this.$q.localStorage.has('items')) {
          console.log('localStorage.has(\'items\') == true')
          try {
            // this.$q.localStorage.remove('items')
            this.$q.localStorage.clear()
            let parsed = JSON.stringify([])
            this.$q.localStorage.set('items', parsed)
            this.$q.notify({message: 'Dados apagados.', color: 'positive'})
            console.log('Dados apagados.')
            this.$forceUpdate()
          } catch (e) {
            console.log('Erro ao apagar os dados' + e)
            this.$q.notify('Erro ao apagar dados.')
          }
        } else {
          this.$q.notify({message: 'Nenhum dado a ser apagado.', color: 'warning'})
        }
      }).catch(() => {
        console.log('showDialog: NOK - saveItem')
      })
      // this.$refs.batchId.focus()
    },
    saveInventoryData () {
      let self = this
      self.loadInventoryData()
      if (this.items.length === 0) {
        self.$q.notify({message: 'Nenhum dado a ser enviado.', color: 'warning'})
      } else {
        // self.$axios.post('http://localhost:1880/connector/v1/inventory/export/', this.items)
        self.$axios.post('http://192.168.0.63:1880/connector/v1/inventory/export/', this.items)
          .then(function (response) {
            console.log(response)
            if (response.data.length === 0) {
              self.$q.notify('Erro ao enviar dados')
            } else {
              self.$q.notify({message: 'Dados enviados!', color: 'positive'})
            }
            self.$forceUpdate()
          })
          .catch(function (error) {
            console.log(error)
          })
      }
      self.$forceUpdate()
    }
  }
}
</script>

<style>
</style>
