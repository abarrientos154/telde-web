<template>
  <q-layout view="hHh Lpr lFf">
    <q-header elevated>
        <q-toolbar class="row justify-between">
          <div>
            <q-img src="logo_nova_telde.png" style="width:220px; height:80px" />
          </div>

          <div v-if="rol !== 0">
            <div v-if="rol !== 1" class="row justify-around items-center q-gutter-md" >
              <div><q-btn :label="rol === 3 ? 'Tienda' : 'Inicio'" color="white" flat dense no-caps size="lg" @click="rol === 3 ? $router.push('/tienda/'+user_id) : $router.push('/inicio')" /></div>
              <div><q-btn :label="rol === 3 ? 'Monedero' : 'Tiendas'" color="white" flat dense no-caps size="lg" @click="rol === 3 ? $router.push('/monedero/') : $router.push('/tiendas')" /></div>
              <div><q-btn :label="rol === 3 ? 'Estadísticas' : 'Favoritos'" color="white" flat dense no-caps size="lg" @click="rol === 2 ? $router.push('/tiendas_favoritas') : $router.push('/estadisticas')" /></div>
              <div><q-btn label="Pedidos" color="white" flat dense no-caps size="lg" @click="rol === 3 ? $router.push('/mis_pedidos_tienda') : $router.push('/mis_pedidos')" /></div>
              <div><q-btn label="Cerrar sesión" color="white" flat dense no-caps size="lg" @click="cerrarSesion()" /></div>
            </div>
            <div v-else class="row justify-around items-center q-gutter-md" >
              <div><q-btn label="Home" color="white" flat dense no-caps size="lg" @click="$router.push('/administrador')" /></div>
              <div><q-btn label="Usuarios" color="white" flat dense no-caps size="lg" @click="$router.push('/usuarios')" /></div>
              <div><q-btn label="Retiros" color="white" flat dense no-caps size="lg" @click="$router.push('/retiros')" /></div>
              <div><q-btn label="Estadísticas" color="white" flat dense no-caps size="lg" @click="$router.push('/reportes')" /></div>
              <div><q-btn label="Cerrar sesión" color="white" flat dense no-caps size="lg" @click="cerrarSesion()" /></div>
            </div>
          </div>

          <div v-else class="row justify-around items-center q-gutter-md">
            <div><q-btn label="Inicio" color="white" flat dense no-caps size="lg" @click="$router.push('/inicio')" /></div>
            <div><q-btn label="Iniciar sesión" color="white" flat dense no-caps size="lg" @click="$router.push('/login')" /></div>
          </div>
        </q-toolbar>
      </q-header>

    <q-page-container class="q-mb-md">
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { mapGetters, mapMutations } from 'vuex'
export default {
  name: 'MainLayout',
  data () {
    return {
      rol: 0,
      text: '',
      user_id: ''
    }
  },
  computed: {
    ...mapGetters('generals', ['can'])
  },
  mounted () {
    const value = localStorage.getItem('TELDE_SESSION_INFO')
    if (value) {
      this.getInfo()
    }
  },
  methods: {
    ...mapMutations('generals', ['logout']),
    cerrarSesion () {
      this.logout()
      this.$router.push('/login')
    },
    getInfo () {
      this.$api.get('user_info').then(res => {
        if (res) {
          this.rol = res.roles[0]
          this.user_id = res._id
        }
      })
    }
  }
}
</script>

<style>
</style>
