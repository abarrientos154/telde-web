<template>
  <q-layout view="hHh Lpr lff">
    <q-header elevated>
        <q-toolbar class="row justify-between">
          <div class="row">
            <q-btn v-if="!web" flat @click="drawer = !drawer" round dense color="grey-8" icon="menu" />
            <q-img src="logo_nova_telde.png" style="width:220px; height:80px" />
          </div>

          <div v-if="web">
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
          </div>
        </q-toolbar>
      </q-header>

      <q-drawer
        v-model="drawer"
        :width="220"
        :breakpoint="500"
        bordered
        overlay
      >
        <q-scroll-area class="fit bg-grey-3">
          <q-list>

            <div class="q-py-sm" v-for="(item, index) in menu" :key="index">
              <q-item clickable v-ripple @click="item.label != 'Cerrar sesión' ? $router.push(item.ruta) : cerrarSesion()">
                <q-item-section avatar>
                  <q-icon color="grey-7" :name="item.icon" />
                </q-item-section>
                <q-item-section>
                  {{ item.label }}
                </q-item-section>
              </q-item>
              <q-separator />
            </div>

          </q-list>
        </q-scroll-area>
      </q-drawer>

    <q-page-container class="q-mb-md">
      <router-view />
    </q-page-container>

       <div footer class="row items-center justify-between bg-black text-white">
         <div class="row justify-center col-xs-12 col-sm-4 col-md-4 col-lg-4 col-xl-4">
           <q-img src="novafooter.png" style="width:220px; height:220px; border-radius:100%" />
         </div>
         <div class="column items-center q-pb-md col-xs-12 col-sm-4 col-md-4 col-lg-4 col-xl-4">
            <div class="text-bold q-pb-md" @click="getFile()">Términos y condiciones de uso</div>
         </div>
         <div class="self-end col-xs-12 col-sm-4 col-md-4 col-lg-4 col-xl-4">
           <div class="text-bold q-pb-sm text-center">Todos los derechos reservados Nova Telde</div>
         </div>
       </div>
  </q-layout>
</template>

<script>
import { openURL } from 'quasar'
import { mapGetters, mapMutations } from 'vuex'
export default {
  name: 'MainLayout',
  data () {
    return {
      web: false,
      drawer: false,
      rol: 0,
      text: '',
      user_id: '',
      menu: [],
      menuNoLogin: [
        { label: 'Inicio', ruta: '/inicio', icon: 'home' },
        { label: 'Iniciar sesión', ruta: '/login', icon: 'login' }
      ]
    }
  },
  computed: {
    ...mapGetters('generals', ['can'])
  },
  mounted () {
    this.web = this.$q.platform.is.desktop
    const value = localStorage.getItem('TELDE_SESSION_INFO')
    if (value) {
      this.getInfo()
    } else {
      this.menu = this.menuNoLogin
    }
  },
  methods: {
    ...mapMutations('generals', ['logout']),
    cerrarSesion () {
      this.logout()
      this.$router.push('/login')
    },
    getFile () {
      openURL('https://app.novatelde.com/pdf_condiciones.pdf')
    },
    ver () {
      console.log('hola')
    },
    getInfo () {
      this.$api.get('user_info').then(res => {
        if (res) {
          this.rol = res.roles[0]
          this.user_id = res._id
          if (this.rol === 1) {
            this.menu = [
              { label: 'Home', ruta: '/administrador', icon: 'home' },
              { label: 'Usuarios', ruta: '/usuarios', icon: 'groups' },
              { label: 'Retiros', ruta: '/retiros', icon: 'monetization_on' },
              { label: 'Estadísticas', ruta: '/reportes', icon: 'description' },
              { label: 'Cerrar sesión', ruta: '', icon: 'logout' }
            ]
          } else if (this.rol === 2) {
            this.menu = [
              { label: 'Home', ruta: '/inicio', icon: 'home' },
              { label: 'Tiendas', ruta: '/tiendas', icon: 'store' },
              { label: 'Favoritos', ruta: '/tiendas_favoritas', icon: 'favorite' },
              { label: 'Pedidos', ruta: '/mis_pedidos', icon: 'shopping_basket' },
              { label: 'Cerrar sesión', ruta: '', icon: 'logout' }
            ]
          } else {
            this.menu = [
              { label: 'Tienda', ruta: '/tienda/' + this.user_id, icon: 'store' },
              { label: 'Monedero', ruta: '/monedero/', icon: 'monetization_on' },
              { label: 'Estadísticas', ruta: '/estadisticas', icon: 'description' },
              { label: 'Pedidos', ruta: '/mis_pedidos_tienda', icon: 'shopping_basket' },
              { label: 'Cerrar sesión', ruta: '', icon: 'logout' }
            ]
          }
        }
      })
    }
  }
}
</script>

<style>
</style>
