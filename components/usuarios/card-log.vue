<template>
  <v-card shaped elevation="5" width="600" color="#E0F7FA">
    <v-card-title style="justify-content: center; color: black;">
      Login Firebase
    </v-card-title>
    <v-card-text>
      <v-form ref="frm">
        <v-text-field
          v-model="correo"
          label="Email"
          placeholder="Correo"
          :rules="validateEmail"
          color="purple"
          class="custom-placeholer-color custom-label-color"
        />
        <v-text-field
          v-model="contra"
          label="Password"
          placeholder="Escribe tu Contraseña"
          type="password"
          :rules="validatePassword"
          color="purple"
          class="custom-label-color custom-placeholer-color"
        />
      </v-form>
    </v-card-text>
    <v-card-actions>
      <v-btn block class="colorBtn" @click="entrar">
        Ingresar
        <i class="mdi-chevron-right mdi v-icon notranslate v-theme--light v-icon--size-default v-icon--end" aria-hidden="true" />
      </v-btn>
    </v-card-actions>
    <v-dialog v-model="dialog" width="350">
      <div style="width: 100%; height: 150px; background-color: #7a0012; text-align: center; padding-top: 10%;">
        <span style="color: antiquewhite; font-size: 1.3rem;">{{ mensaje }}</span>
      </div>
    </v-dialog>
  </v-card>
</template>

<script>
export default {
  data () {
    return {
      correo: '',
      contra: '',
      validateEmail: [
        v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'Introduce un correo válido'
      ],
      validatePassword: [
        v => !v || v.length >= 6 || 'La contraseña debe tener minimo 6 caracteres'
      ],
      dialog: false,
      mensaje: ''
    }
  },
  methods: {
    async entrar () {
      if (this.correo.length === 0 && this.contra.length === 0) {
        alert('Escribe un correo y una contraseña')
        return
      }
      if (this.$refs.frm.validate()) {
        const sendData = {
          email: this.correo,
          password: this.contra
        }
        await this.$auth.loginWith('local', {
          data: sendData
        }).then((res) => {
          if (res.data.error === null) {
            this.$router.push('/dashboard')
          }
        }).catch((error) => {
          this.mensaje = 'Introduce un correo y contraseña validos'
          this.dialog = true
          setTimeout(() => {
            this.dialog = false
          }, 2000)
          console.log(error)
        })
      } else {
        alert('Introduce un correo y contraseña validos')
      }
    }
  }
}
</script>

  <style>

  .colorBtn{
    background-color: black!important;
    color: antiquewhite!important;
  }

  .v-dialog__container{
    display: flex;
  }

.custom-placeholer-color input::placeholder {
  color: black!important;
  opacity: 1;
}

.custom-label-color .v-label {
  color: black!important;
  opacity: 1;
}

.custom-placeholer-color input,
.custom-label-color input{
  color: black!important;
}

  </style>
