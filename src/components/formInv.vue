<script>
import {
  QLayout,
  QToolbar,
  QToolbarTitle,
  QBtn,
  QIcon,
  QList,
  QListHeader,
  QInput,
  QItem,
  QItemSide,
  QItemMain,
  QItemTile,
  Toast
} from 'quasar'

export default {
  components: {
    QLayout,
    QToolbar,
    QToolbarTitle,
    QBtn,
    QIcon,
    QList,
    QListHeader,
    QInput,
    QItem,
    QItemSide,
    QItemMain,
    QItemTile,
    Toast
  },
  data () {
    return {
      batch: {
        id: '',
        warehouse: '',
        block: '',
        square: '',
        package: ''
      },
      packedItem: {
        lote: '',
        id_item_embalado: '',
        epc: '',
        gtin: '',
        descricao: '',
        sku: '',
        data_leitura: '',
        qtde_volume: '',
        data_alocacao: '',
        status: ''
      }
    }
  },
  computed: {


  },
  methods: {
    preventSubmit () {
      console.log('Prevent Submit.')
    },
    cleanUpData () {
      //  this.$store.commit('CLEAR_ITEMS')

    },
    scanBarcode () {
      var self = this
      cordova.plugins.barcodeScanner.scan(
        function (result) {
          self.batch.id = result.text
        },
        function (error) {
          Toast.create.warning({
            html: 'Erro ao ler o código de barras.' + error
          })
        }
      )
    },
    reset () {
      this.batch.id = ''
    }
  }
}
</script>

<template>

  <form @submit.prevent="preventSubmit">
    <table class="q-table">
      <tbody>
      <tr class="my-table">
        <td class="my-table"><q-input v-model="batch.id"  type="number"  placeholder="999999" float-label="Lote"/> </td>
        <td class="my-table"><q-btn icon="camera_enhance" color="primary" @click="scanBarcode">Scan</q-btn></td>
        <td class="my-table"><q-btn icon="clear" color="primary" @click="cleanUpData">Limpar</q-btn></td>
      </tr>
      <tr>
        <td class="text-left">
          <q-input ref="warehouseField" v-model="warehouseField" type="number" placeholder="XX" float-label="Depósito"
                   @keydown="fieldLenValidatorWarehouseField" :max-length="2"/>
        </td>
        <td class="text-left">
          <q-input ref="blockField" v-model="blockField" type="number" placeholder="XX" float-label="Bloco"
                   @keydown="fieldLenValidatorBlockField" :max-length="2"/>
        </td>
        <td class="text-left">
          <q-input ref="squareField" v-model="squareField" type="number" placeholder="X" float-label="Quadra"
                   @keydown="fieldLenValidatorSquareField" :max-length="1"/>
        </td>
        <td class="text-left">
          <q-input ref="packageField" v-model="packageField" type="number" placeholder="XX" float-label="Lote"
                   @keydown="fieldLenValidatorPackageField" :max-length="2"/>
        </td>
        <td class="text-left">
          <q-input ref="floorField" v-model="floorField" type="number" placeholder="X" float-label="Andar"
                   @keydown="fieldLenValidatorFloorField" :max-length="1"/>
        </td>
      </tr>

      </tbody>
    </table>
    <q-btn icon="search" color="primary" class="my-button" @click="submit">[Buscar]</q-btn>

  </form>

</template>

<style scoped>
  .my-table {
    width: 100%;
    margin-bottom: 20px;
  }

  .my-input {
    width: 100%;
    margin-bottom: 20px;
  }

  .my-button{
    width: 100%;
  }
</style>
