<style>
</style>

<script>
/* eslint-disable no-undef */

document.addEventListener('deviceready', () => {}, false)
// function onDeviceReady () {
//   console.log(cordova.file)
// }
// var logOb

// function createFile (dirEntry, fileName, isAppend) {
//   // Creates a new file or returns the file if it already exists.
//   dirEntry.getFile(fileName, {create: true, exclusive: false}, function (fileEntry) {
//     writeFile(fileEntry, null, isAppend)
//   }, onErrorCreateFile)
// }

function writeFile (fileEntry, dataObj, isAppend) {
  // Create a FileWriter object for our FileEntry (log.txt).
  fileEntry.createWriter(function (fileWriter) {
    fileWriter.onwriteend = function () {
      // console.log('Successful file read...')
      // readFile(fileEntry)
    }

    fileWriter.onerror = function (e) {
      console.log('Failed file read: ' + e.toString())
    }

    // If we are appending data to file, go to the end of the file.
    if (isAppend) {
      try {
        fileWriter.seek(fileWriter.length)
      } catch (e) {
        console.log('file doesnt exist!')
      }
    }
    fileWriter.write(dataObj)
  })
}

import { required } from 'vuelidate/lib/validators'
// import { Toast } from 'quasar'

export default {
  name: 'PageIndex',
  data: function () {
    return {
      errors: [],
      customDialogModel: false,
      items: [],
      itemCount: 0,
      newItem: null,
      batchId: '',
      qtyStackField: '',
      qtyStackFullField: '',
      qtyIndividualItemsField: '',
      qtyTotalField: '',
      barcodeField: '',
      barcodePositionField: '',
      warehouseField: '',
      blockField: '',
      squareField: '',
      packageField: '',
      floorField: '',
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
  computed: {},
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
    this.itemCount = this.items.length
    // this.$refs.batchId.focus()
  },
  validations: {
    batchId: { required }
  },
  methods: {
    refresher (done) {
      this.items = []
      this.itemCount = 0
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
      this.itemCount = this.items.length
      this.$q.notify({message: 'Pronto!', color: 'positive'})
      done()
    },
    addItem () {
      // ensure they actually typed something
      if (!this.newItem) {
        return
      }
      this.items.push(this.newItem)
      this.newItem = ''
      this.saveItems()
    },
    removeItem (x) {
      this.items.splice(x, 1)
      this.saveItems()
    },
    saveItems () {
      const parsed = JSON.stringify(this.items)
      this.$q.localStorage.set('items', parsed)
      // localStorage.setItem('items', parsed)
    },
    manualSearchItem () {
      this.searchItem()
      if (this.newItem === '') {
        this.$q.notify({message: 'Leitura anterior não encontrada.', color: 'info'})
      } else {
        this.$q.notify({message: 'Atualizando leitura anterior!', color: 'positive'})
      }
      this.$refs.qtyStackField.focus()
    },
    searchItem () {
      this.$nextTick(() => {
        console.log('searchItem>> ' + this.barcodeField)
        this.newItem = ''
        for (let i = 0; i < this.items.length; i++) {
          if (this.items[i].barcodeField === this.barcodeField) {
            if ((parseInt(this.items[i].batchId) === parseInt(this.batchId)) &&
              (parseInt(this.items[i].barcodePositionField) === parseInt(this.barcodePositionField)) &&
              (parseInt(this.items[i].floorField) === parseInt(this.floorField))) {
              console.log('searchItem: current item found ' + this.batchId)
              console.log('searchItem: current item found ' + this.barcodePositionField)
              console.log('searchItem: current item found ' + this.floorField)
              console.log('searchItem: current item found ' + this.barcodeField)
              var currentItem = {}
              currentItem.qtyStackField = this.items[i].qtyStackField
              currentItem.qtyStackFullField = this.items[i].qtyStackFullField
              currentItem.qtyIndividualItemsField = this.items[i].qtyIndividualItemsField
              currentItem.qtyTotalField = this.items[i].qtyTotalField
              currentItem.barcodeField = this.items[i].barcodeField
              currentItem.barcodePositionField = this.items[i].barcodePositionField
              currentItem.warehouseField = this.items[i].warehouseField
              currentItem.blockField = this.items[i].blockField
              currentItem.squareField = this.items[i].squareField
              currentItem.packageField = this.items[i].packageField
              currentItem.floorField = this.items[i].floorField
              this.newItem = currentItem
              this.barcodePositionField = this.newItem.barcodePositionField
              this.warehouseField = this.newItem.warehouseField
              this.blockField = this.newItem.blockField
              this.squareField = this.newItem.squareField
              this.packageField = this.newItem.packageField
              this.floorField = this.newItem.floorField
              this.qtyStackField = this.newItem.qtyStackField
              this.qtyStackFullField = this.newItem.qtyStackFullField
              this.qtyIndividualItemsField = this.newItem.qtyIndividualItemsField
              this.qtyTotalField = this.newItem.qtyTotalField
              this.$q.notify({message: 'Atualizando leitura anterior!', color: 'positive'})
              return this.items[i]
            }
          }
        }
      })
      // console.log(json[obj].name) // compare this with your "searchtext
    },
    saveItem () {
      this.qtyTotalField = ((this.qtyStackField * this.qtyStackFullField) + this.qtyIndividualItemsField)
      if (this.warehouseField.length === 1) {
        this.warehouseField = '0' + this.warehouseField
      }
      if (this.blockField.length === 1) {
        this.blockField = '0' + this.blockField
      }
      if (this.packageField.length === 1) {
        this.packageField = '0' + this.packageField
      }
      console.log('saveItem...')
      var currentItem = {}
      currentItem.batchId = this.batchId
      currentItem.qtyStackField = this.qtyStackField
      currentItem.qtyStackFullField = this.qtyStackFullField
      currentItem.qtyIndividualItemsField = this.qtyIndividualItemsField
      currentItem.qtyTotalField = this.qtyTotalField
      currentItem.barcodeField = this.barcodeField
      currentItem.barcodePositionField = this.barcodePositionField
      currentItem.warehouseField = this.warehouseField
      currentItem.blockField = this.blockField
      currentItem.squareField = this.squareField
      currentItem.packageField = this.packageField
      currentItem.floorField = this.floorField
      this.newItem = currentItem
      if (this.items.length > 0) {
        let itemExists = false
        for (let i = 0; i < this.items.length; i++) {
          if (this.items[i].barcodeField === this.newItem.barcodeField) {
            if ((parseInt(this.items[i].batchId) === parseInt(this.batchId)) &&
              (parseInt(this.items[i].barcodePositionField) === parseInt(this.barcodePositionField)) &&
              (parseInt(this.items[i].floorField) === parseInt(this.floorField))) {
              this.items[i].batchId = this.batchId
              this.items[i].qtyStackField = this.qtyStackField
              this.items[i].qtyStackFullField = this.qtyStackFullField
              this.items[i].qtyIndividualItemsField = this.qtyIndividualItemsField
              this.items[i].qtyTotalField = this.qtyTotalField
              this.items[i].barcodeField = this.barcodeField
              this.items[i].barcodePositionField = this.barcodePositionField
              this.items[i].warehouseField = this.warehouseField
              this.items[i].blockField = this.blockField
              this.items[i].squareField = this.squareField
              this.items[i].packageField = this.packageField
              this.items[i].floorField = this.floorField
              itemExists = true
              this.saveItems()
              break
            }
          }
        }
        if (!itemExists) {
          console.log('saveItem: !itemExists -> NEW ITEM!')
          this.items.push(this.newItem)
          this.newItem = ''
          this.saveItems()
        }
      } else {
        console.log('saveItem: creating list')
        this.items.push(this.newItem)
        this.saveItems()
        this.newItem = ''
      }
      this.itemCount = this.items.length
      // this.saveToFile()
      // console.log(json[obj].name) // compare this with your "searchtext
    },
    fieldLenValidatorBarcodePositionField: function (evt) {
      // var self = this
      // var currentData = this.barcodePositionField
      this.$nextTick(() => {
        let containsLineBreak = false
        let keyCode = 0
        console.log('barcodePositionField length:' + this.barcodePositionField.toString().length)
        // console.log('currentData length:' + currentData.toString().length)
        console.log('barcodePositionField:' + this.barcodePositionField)
        if (evt.key !== undefined) {
          keyCode = evt.key
        } else if (evt.keyIdentifier !== undefined) {
          keyCode = evt.keyIdentifier
        } else if (evt.keyCode !== undefined) {
          keyCode = evt.keyCode
        }
        if (this.barcodePositionField.toString().indexOf('\n') > -1) {
          containsLineBreak = true
        }
        console.log('fieldLenValidatorBarcodePositionField:' + keyCode)
        // this.barcodePositionField.length > 6 ||
        if (containsLineBreak || this.barcodePositionField.toString().length > 7 || keyCode === 13 || keyCode === 9 || keyCode === 'Tab' || keyCode === 'Enter') {
          this.warehouseField = this.barcodePositionField.toString().substring(0, 2)
          this.blockField = this.barcodePositionField.toString().substring(2, 4)
          this.squareField = this.barcodePositionField.toString().substring(4, 5)
          this.packageField = this.barcodePositionField.toString().substring(5, 7)
          if (this.warehouseField.toString().length === 1) {
            this.warehouseField = '0' + this.warehouseField
          }
          if (this.blockField.toString().length === 1) {
            this.blockField = '0' + this.blockField
          }
          if (this.packageField.toString().length === 1) {
            this.packageField = '0' + this.packageField
          }
          if (this.barcodePositionField.toString().length >= 8) {
            this.floorField = this.barcodePositionField.toString().substring(7, 8)
          }
          this.$refs.floorField.focus()
        }
      })
    },
    // fieldLenValidatorBarcodePositionField (evt) {
    //   // var self = this
    //   let keyCode = 0
    //   console.log('barcodePositionField length:' + this.barcodePositionField.length)
    //   console.log('barcodePositionField:' + this.barcodePositionField)
    //   if (evt.key !== undefined) {
    //     keyCode = evt.key
    //   } else if (evt.keyIdentifier !== undefined) {
    //     keyCode = evt.keyIdentifier
    //   } else if (evt.keyCode !== undefined) {
    //     keyCode = evt.keyCode
    //   }
    //   console.log('fieldLenValidatorBarcodePositionField:' + keyCode)
    //   // this.barcodePositionField.length > 6 ||
    //   if (keyCode === 13 || keyCode === 9 || keyCode === 'Tab' || keyCode === 'Enter') {
    //     this.warehouseField = this.barcodePositionField.toString().substring(0, 2)
    //     this.blockField = this.barcodePositionField.toString().substring(2, 4)
    //     this.squareField = this.barcodePositionField.toString().substring(4, 5)
    //     this.packageField = this.barcodePositionField.toString().substring(5, 7)
    //     if (this.warehouseField.length === 1) {
    //       this.warehouseField = '0' + this.warehouseField
    //     }
    //     if (this.blockField.length === 1) {
    //       this.blockField = '0' + this.blockField
    //     }
    //     if (this.packageField.length === 1) {
    //       this.packageField = '0' + this.packageField
    //     }
    //     this.$refs.floorField.focus()
    //   }
    //   // if (evt.keyCode === 13) {
    //   //   self.$refs.floorField.focus()
    //   // }
    //   // if (this.barcodePositionField.length >= 7) {
    //   //   evt.preventDefault()
    //   // }
    // },
    fieldLenValidatorBarcodeField (evt) {
      // var self = this
      let keyCode = 0
      let containsLineBreak = false
      if (event.key !== undefined) {
        keyCode = event.key
      } else if (event.keyIdentifier !== undefined) {
        keyCode = event.keyIdentifier
      } else if (event.keyCode !== undefined) {
        keyCode = event.keyCode
      }
      // if (!keyCode.match(/[0-9]+/)) {
      //   containsLineBreak = true
      // }
      // if (!isNaN(keyCode)) {
      //   containsLineBreak = true
      // }
      if (this.barcodeField.toString().indexOf('\n') > -1) {
        containsLineBreak = true
      }
      console.log('fieldLenValidatorBarcodeField:' + keyCode)
      if (containsLineBreak || this.barcodeField.toString().length > 7 || keyCode === 13 || keyCode === 9 || keyCode === 'Tab' || keyCode === 'Enter') {
        console.log('fieldLenValidatorBarcodeField: searchItem')
        this.searchItem()
      }
      if (containsLineBreak || keyCode === 13 || keyCode === 9 || keyCode === 'Tab' || keyCode === 'Enter') {
        this.$refs.qtyStackField.focus()
      }
    },
    fieldLenValidatorBatchIdField (evt) {
      // var self = this
      let keyCode = 0
      let containsLineBreak = false
      if (event.key !== undefined) {
        keyCode = event.key
      } else if (event.keyIdentifier !== undefined) {
        keyCode = event.keyIdentifier
      } else if (event.keyCode !== undefined) {
        keyCode = event.keyCode
      }
      if (this.batchId.toString().indexOf('\n') > -1) {
        containsLineBreak = true
      }
      // if (!isNaN(keyCode)) {
      //   containsLineBreak = true
      // }
      console.log('fieldLenValidatorBatchIdField keyCode:' + keyCode)
      console.log('this.batchId.toString().length :' + this.batchId.toString().length)
      if (containsLineBreak || this.batchId.toString().length === 6 || keyCode === 13 || keyCode === 9 || keyCode === 'Tab' || keyCode === 'Enter') {
        console.log('fieldLenValidatorBatchIdField: barcodePositionField focus')
        this.$refs.barcodePositionField.focus()
      }
    },
    preventSubmit () {
      console.log('Prevent Submit.')
    },
    cleanUpData () {
      //  this.$store.commit('CLEAR_ITEMS')
    },
    cleanUpScreen () {
      this.qtyStackField = ''
      this.qtyStackFullField = ''
      this.qtyIndividualItemsField = ''
      this.qtyTotalField = 0
      this.batchId = ''
      this.barcodeField = ''
      this.barcodePositionField = ''
      this.warehouseField = ''
      this.blockField = ''
      this.squareField = ''
      this.packageField = ''
      this.floorField = ''
      this.$refs.batchId.focus()
    },
    resumeNextProduct () {
      this.qtyStackField = ''
      this.qtyStackFullField = ''
      this.qtyIndividualItemsField = ''
      this.qtyTotalField = 0
      this.barcodeField = ''
      this.newItem = ''
      this.$refs.barcodeField.focus()
    },
    scanBarcode () {
      var self = this
      cordova.plugins.barcodeScanner.scan(
        function (result) {
          self.batchId = result.text
        }
      )
    },
    reset () {
      this.batchId = ''
    },
    validateBatchId () {
      if (this.batchId.toString().length < 1) {
        this.$q.notify('Unitizador é requerido!')
        this.$refs.batchId.focus()
      }
    },
    showDialog () {
      console.log('showDialog')
      if (this.batchId.toString().length < 1) {
        this.$q.notify('Unitizador é requerido!')
      } else if (this.barcodePositionField.toString().length < 1) {
        this.$q.notify('Endereço é requerido!')
      } else if (this.floorField.toString().length < 1) {
        this.$q.notify('Andar é requerido!')
      } else if (this.barcodeField.toString().length < 1) {
        this.$q.notify('Código do produto é requerido!')
      } else {
        this.$q.dialog({
          title: 'Inventário',
          message: 'Há mais produtos na mesma posição?',
          ok: 'SIM',
          cancel: 'NÃO',
          preventClose: true
        }).then(() => {
          console.log('showDialog: OK - saveItem')
          this.saveItem()
          this.$q.notify({message: 'Processando mais produtos!', color: 'info'})
          this.resumeNextProduct()
        }).catch(() => {
          console.log('showDialog: NOK - saveItem')
          this.saveItem()
          this.$q.notify({message: 'Limpando...', color: 'info'})
          this.cleanUpScreen()
        })
      }
    },
    // jsonToCSV (data) {
    //   var csvData = []
    //   // Fields
    //   for (let key in data[0]) {
    //     csvData.push(key)
    //     csvData.push(',')
    //   }
    //   csvData.pop()
    //   csvData.push('\r\n')
    //   // Data
    //   data.map(function (item) {
    //     for (let key in item) {
    //       let escapedCSV = '="' + item[key] + '"' // cast Numbers to string
    //       if (escapedCSV.match(/[,"\n]/)) {
    //         escapedCSV = '"' + escapedCSV.replace(/"/g, '""') + '"'
    //       }
    //       csvData.push(escapedCSV)
    //       csvData.push(',')
    //     }
    //     csvData.pop()
    //     csvData.push('\r\n')
    //   })
    //   return csvData.join('')
    // },
    saveToFile () {
      // window.resolveLocalFileSystemURL(cordova.file.dataDirectory, function (dirEntry) {
      //   console.log('file system open: ' + dirEntry.name)
      //   var isAppend = true
      //   createFile(dirEntry, 'fileToAppend.txt', isAppend)
      //   writeFile(dirEntry, 'fileToAppend.txt', isAppend)
      // }, onErrorLoadFs)
      window.resolveLocalFileSystemURL(cordova.file.dataDirectory, function (dir) {
        console.log('diretorio', dir)
        dir.getFile('inventario.csv', {create: true, exclusive: false}, function (file) {
          console.log('handle do arquivo:', file)
          // logOb = file
          // var csv = ''
          // we should have the same amount of name/cookie fields
          var isAppend = true
          // Unitizador;Endereço;Código Item(DUN14;EAN13;Código AX);quantidade
          var csv = this.batchId + ';' + this.warehouseField + this.blockField +
            this.squareField + this.packageField + this.floorField + ';' +
            this.barcodeField + ';' + this.qtyTotalField + '\r\n'
          console.log('csv-' + csv)
          writeFile(file, csv, isAppend)
          // writeLog(csv)
        })
      })
    }

  }
}
</script>

<template>

  <q-pull-to-refresh :handler="refresher" >
  <form @submit.prevent="preventSubmit">
    <div align="right"><span class="q-title">Total de registros: {{ itemCount }}</span></div>
    <div class="row">
      <div class="col">
        <q-input ref="batchId" v-model="batchId" type="text" placeholder="999999"
                 numeric-keyboard-toggle float-label="Unitizador" maxlength="10"
                 autofocus="true" error-label="Unitizador obrigatório"
                 @keydown="fieldLenValidatorBatchIdField"/>
      </div>
      <div class="col" align="right"><br/>
        <q-btn icon="clear" color="primary" @click="cleanUpScreen()">Limpar</q-btn>
      </div>
    </div>

    <div class="row">
      <div class="col">
        <q-input ref="barcodePositionField" v-model="barcodePositionField" type="text" placeholder="9999999"
                 float-label="Posição" numeric-keyboard-toggle
                 @keydown="fieldLenValidatorBarcodePositionField" :max-length="128"/>
      </div>

    </div>
    <div class="row">
      <div class="col">
        <q-input ref="warehouseField" v-model="warehouseField" type="text" readonly placeholder="XX" float-label="Depósito"
                 :max-length="2"/>
      </div>
      <div class="col">
        <q-input ref="blockField" v-model="blockField" type="text" readonly placeholder="XX" float-label="Bloco"
                 :max-length="2"/>
      </div>
    </div>

    <div class="row">
      <div class="col">
        <q-input ref="squareField" v-model="squareField" type="text" readonly placeholder="X" float-label="Quadra"
                 :max-length="1"/>
      </div>
      <div class="col">
        <q-input ref="packageField" v-model="packageField" type="text" readonly placeholder="XX" float-label="Lote"
                 :max-length="2"/>
      </div>
    </div>

    <div class="row">
      <div class="col">
        <q-input ref="floorField" v-model="floorField" type="number" placeholder="X" float-label="Andar"
                 :max-length="1"/>
      </div>
      <div class="col"></div>
    </div>

    <div class="row">
      <div class="col">
        <q-input ref="barcodeField" v-model="barcodeField" type="text" placeholder="99999999999999"
                 float-label="Código de Barras" numeric-keyboard-toggle
                 :max-length="128"
                 @keydown="fieldLenValidatorBarcodeField"/>
      </div>
      <div class="col-2" align="center"><br/>
        <q-btn icon="search" color="primary" class="my-button" @click="manualSearchItem()"/>
      </div>

    </div>

    <div class="row">
      <div class="col">
        <q-input ref="qtyStackField" v-model="qtyStackField" type="number" placeholder="999"
                 float-label="Qtde Padrão da Pilha" value="0"
                 :max-length="4"/>
      </div>
      <div class="col">
        <q-input ref="qtyStackFullField" v-model="qtyStackFullField" type="number" placeholder="999"
                 float-label="Qtde Pilhas Completas" value="0"
                 :max-length="4"/>
      </div>
    </div>

    <div class="row">
      <div class="col-3">
        <q-input ref="qtyIndividualItemsField" v-model="qtyIndividualItemsField" type="number" placeholder="9999"
                 float-label="Qtde Itens Avulsos" value="0"
                 :max-length="4" />
      </div>
      <div class="col" align="center"><br/>
        <span class="q-title">{{ ((qtyStackField * qtyStackFullField) + qtyIndividualItemsField) }}</span>
      </div>
      <div class="col" align="center"><br/>
        <q-btn icon="save" color="primary" class="my-button" @click="showDialog()">Gravar</q-btn>
      </div>
    </div>
    <br/>

    <!--q-btn icon="save" color="primary" class="my-button" @click="showDialog()">[Gravar]</q-btn-->

  </form>
  </q-pull-to-refresh>

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

  .my-button {
    width: 100%;
  }
</style>
