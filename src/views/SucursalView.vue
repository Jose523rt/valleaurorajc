<template>
    <div class="min-h-screen bg-slate-50 font-sans">
        <div class="bg-[#1a1a2e] px-10 py-12" >
            <h1 class="text-3xl font-bold trackig-tight text-white mb-2">
                Nuestras Sucursales 
            </h1>
            <p class="text-sm text-slate-400">Ubicación</p>
        </div>
        <div class="flex items-center gap-3 px-8 py-3 bg-white border-b border-slate-200 text-sm">
            <!--Cargando Ubicación-->
            <template v-if="status === 'loading'">
                <div class="w-4 h-4 rounded-full border-2 border-slate-200 border-t-indigo-500 animate-spin"></div>
                <span class="text-slate-500">Obteniendo Ubicación</span>
            </template>
            <!--Error-->
            <template v-else-if="status === 'error'">
                <span class="text-red-500 font-medium">🗺️ {{geoError}}</span>
            </template>
            <!--Coordenadas obtenidas-->
            <template v-else-if="status === 'ok'">
                <span class="inline-block w-2 h-2 rounded-full bg-green-400"></span>
                <span class="text-slate-600">
                    lat: <b class="text-slate-800">{{ coords.lat.toFixed(6) }}</b>&nbsp;&nbsp;
                    lng: <b class="text slate-800">{{ coords.lng.toFixed(6) }}</b>
                </span>
                <button class="ml-auto text-xs font-semibold text-indigo-500"
                @click="centrarMapa">Centrar Mapa</button>
            </template>
        </div>
        <!--Dibujar Mapa-->
        <div class="relative flex-1">
            <!--Overlay-->
            <div v-if="mapaCargando" class="absolute inset-0 flex-col border-slate-200 border-t-indigo-500 animate-spin"></div>
            <span class="text-sm text-slate-400">Cargando Mapa</span>
        </div>
        <div ref="mapaRef" class="w-full h-full"></div>
    </div>
</template>

<script setup>
    import { ref, onMounted, onUnmounted } from 'vue';
    const API_KEY = import.meta.env.VITE_GOOGLE_MAPS_API_KEY;

    /** Estado */

    const mapaRef = ref(null)
    const mapaCargando = ref(true)
    const status = ref('loading') //loading, error, ok
    const geoError = ref('')
    const coords = ref({lat:0,lgn:0})

    let map = null
    let marker = null

    //--- 1
    function cargarMapa(){
        return new Promise((resolve, reject)=>{
            if(window.google?.maps){resolve(); return}
            const cb = '__gmInit'
            window[cb] = ()=>{ delete window[cb]; resolve() }
            const s = document.createElement('script')
            s.src = `https://maps.googleapis.com/maps/api/js?key=${API_KEY}&callback=${cb}&loading=async`
            s.async = true
            s.defer = true
            s.onerror = ()=> reject(new Error('No se puede cargar el mapa.'))
            document.head.appendChild(s)
        })
    }

</script>