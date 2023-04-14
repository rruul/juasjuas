<template>
  <v-row class="principal" style="padding:10px; margin:10px">
    <v-btn icon block color="#E1F5FE" @click="dialogNuevo()">
      <v-icon dense style="padding-right: 30px;">
        mdi-account-plus
      </v-icon> Agregar Alumno
    </v-btn>
    <v-col cols="12">
      <v-row class="principal">
        Alumnos
      </v-row>
      <v-row class="principal">
        <v-data-table :headers="headers" :items="alumnos" style="width: 100%; align-items: center;">
          <template #[`item.acciones`]="{ item }">
            <v-row>
              <v-col cols="6">
                <v-btn icon color="#F48FB1" @click="dialogU(item)">
                  <v-icon>mdi-account-edit</v-icon>
                </v-btn>
              </v-col>
              <v-col cols="6">
                <v-btn icon color="red" @click="dialogDelete(item)">
                  <v-icon>mdi-eraser</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </template>
        </v-data-table>
      </v-row>
    </v-col>
    <v-dialog
      v-model="dialogBorrado"
      max-width="290"
      persistent
    >
      <v-card>
        <v-card-title class="text-h5">
          Borrado de alumno
        </v-card-title>

        <v-card-text>
          Estas seguro que deseas borrar el alumno seleccionado?
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn
            color="green darken-1"
            text
            @click="dialogBorrado = false"
          >
            Cancelar
          </v-btn>
          <v-btn
            color="red darken-1"
            text
            @click="borrar()"
          >
            Aceptar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog
      v-model="dialogUpdate"
      max-width="400"
      persistent
    >
      <v-card>
        <v-card-title>Actualización de información</v-card-title>
        <v-card-text>
          <v-form ref="frmUpdate">
            Nombre:
            <v-text-field
              v-model="alumno.name"
              placeholder="Nombre"
              :rules="reglaNombre"
            />
            Apellidos:
            <v-text-field
              v-model="alumno.lastname"
              placeholder="Apellidos"
            />
            Numero:
            <v-text-field
              v-model="alumno.number"
              placeholder="Numero"
              :rules="validateNumber"
            />
            Contraseña:
            <v-text-field
              v-model="password"
              placeholder="Contraseña"
              type="password"
              :rules="reglaPassword"
            />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="warning" @click="dialogUpdate=false">
            Cancelar
          </v-btn>
          <v-btn color="green" @click="update">
            Actualizar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog
      v-model="dialogNew"
      max-width="400"
      persistent
    >
      <v-card>
        <v-card-title>Creacion de Nuevo usuario</v-card-title>
        <v-card-text>Introduce la informacion solicitada por el formulario</v-card-text>
        <v-card-text>
          <v-form ref="frmNuevo">
            Correo:
            <v-text-field
              v-model="email"
              placeholder="Correo"
              :rules="validateEmail"
            />
            Nombre:
            <v-text-field
              v-model="name"
              placeholder="Nombre"
              :rules="reglaNombre"
            />
            Apellidos:
            <v-text-field
              v-model="lastname"
              placeholder="Apellidos"
            />
            Numero:
            <v-text-field
              v-model="number"
              placeholder="Numero"
              :rules="validateNumber"
            />
            Password:
            <v-text-field
              v-model="password"
              placeholder="Contraseña"
              type="password"
              :rules="reglaPassword"
            />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="warning" @click="dialogNew=false">
            Cancelar
          </v-btn>
          <v-btn color="green" @click="Agregar">
            Agregar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
export default {
  data () {
    return {
      alumnos: [],
      headers: [
        {
          text: 'Nombres',
          sortable: true,
          value: 'name'
        },
        {
          text: 'Apellidos',
          value: 'lastname'
        },
        {
          text: 'Correo',
          value: 'email'
        },
        {
          text: 'Numero',
          sortable: true,
          value: 'number'
        },
        {
          text: 'Acciones',
          value: 'acciones'
        }
      ],
      dialogBorrado: false,
      emailBorrar: '',
      name: '',
      lastname: '',
      number: '',
      email: '',
      dialogUpdate: false,
      dialogNew: false,
      alumno: {},
      password: '',
      reglaNombre: [
        v => !v || v.length >= 3 || 'Name must have min 3 char'
      ],
      reglaPassword: [
        v => !v || v.length >= 6 || 'Password must have min 6 char'
      ],
      validateEmail: [
        v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
      ],
      validateNumber: [
        v => !v || v.length === 10 || 'El numero debe ser de 10 caracteres'
      ]
    }
  },
  created () {
    this.loadAlumnos()
  },
  methods: {
    async loadAlumnos () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      await this.$axios.get('/traertodo', config)
        .then((res) => {
          this.alumnos = res.data.data
        }).catch((error) => {
          console.log(error)
        })
    },
    dialogDelete (item) {
      this.emailBorrar = item.email
      this.dialogBorrado = true
    },
    async borrar () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const sendData = {
        email: this.emailBorrar
      }
      await this.$axios.post('/eliminar', sendData, config)
        .then((res) => {
          if (res.data.error == null) {
            this.dialogBorrado = false
            this.loadAlumnos()
          } else {
            alert(res.data.data)
          }
        })
        .catch((e) => {
          console.log(e)
        })
    },
    dialogU (item) {
      this.alumno = item
      this.dialogUpdate = true
    },
    async update () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const alumnoUpdate = {
        email: this.alumno.email,
        name: this.alumno.name,
        lastname: this.alumno.lastname,
        password: this.alumno.password,
        number: this.alumno.number
      }
      await this.$axios.post('/actualizar', alumnoUpdate, config)
        .then((res) => {
          console.log(res)
          this.dialogUpdate = false
          this.loadAlumnos()
        }).catch((e) => {
          console.log(e)
        })
    },
    async Agregar () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const sendData = {
        email: this.email,
        name: this.name,
        lastname: this.lastname,
        number: this.number,
        password: this.password
      }
      await this.$axios.post('/insertar', sendData, config)
        .then((res) => {
          if (res.data.error == null) {
            this.dialogNew = false
            this.loadAlumnos()
          } else {
            alert(res.data.data)
          }
        })
        .catch((e) => {
          console.log(e)
        })
    },
    dialogNuevo () {
      this.dialogNew = true
    }
  }
}
</script>
<style scoped>
.principal{
    width: 100%;
}
</style>
