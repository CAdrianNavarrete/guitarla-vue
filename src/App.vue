    <script setup>
    import {ref, reactive, onMounted, watch} from 'vue'
    import {db} from './data/guitarras.js'
    import Guitarra from './components/Guitarra.vue'
    import Header from './components/Header.vue'
    import Footer from './components/Footer.vue'

    // Declaración de variables reactivas
    const guitarras = ref([]) // Lista de guitarras
    const carrito = ref([]) // Lista de productos en el carrito
    const guitarra = ref({}) // Guitarra seleccionada

    // Observador para guardar el carrito en el almacenamiento local cuando cambie
    watch(carrito, () => {
        guardarLocalStorage()
    }, {
        deep: true
    })

    // Función que se ejecuta cuando el componente se monta
    onMounted(() => {
        guitarras.value = db // Asigna la lista de guitarras desde el archivo de datos
        guitarra.value = db[3] // Asigna una guitarra específica desde la lista de guitarras

        // Recupera el carrito del almacenamiento local si existe
        const carritoLocal = localStorage.getItem('carrito')
        if (carritoLocal) {
            carrito.value = JSON.parse(carritoLocal)
        }
    })

    // Función para guardar el carrito en el almacenamiento local
    const guardarLocalStorage = () => {
        localStorage.setItem('carrito', JSON.stringify(carrito.value))
    }

    // Función para agregar un producto al carrito
    const agregarCarrito = (guitarra) => {
        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
        
        if (existeCarrito >= 0 ) {
            carrito.value[existeCarrito].cantidad++
        } else {
            guitarra.cantidad = 1;  
            carrito.value.push(guitarra);
        }
    }

    // Función para decrementar la cantidad de un producto en el carrito
    const decrementarCantidad = (id) => {
        const index = carrito.value.findIndex(producto => producto.id === id)
        if (carrito.value[index].cantidad <= 1) return
        
        carrito.value[index].cantidad--
    }

    // Función para incrementar la cantidad de un producto en el carrito
    const incrementarCantidad = (id) => {
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad >= 5) return
        carrito.value[index].cantidad++
    }

    // Función para eliminar un producto del carrito
    const eliminarProducto = (id) => {
        carrito.value = carrito.value.filter(producto => producto.id !== id)
    }

    // Función para vaciar el carrito
    const vaciarCarrito = () => {
        carrito.value = []
    }
    </script>

    <template>
    <Header
        :carrito = "carrito"
        :guitarra = "guitarra"
        @decrementar-cantidad = "decrementarCantidad"
        @incrementar-cantidad = "incrementarCantidad"
        @agregar-carrito = "agregarCarrito"
        @eliminar-producto = "eliminarProducto"
        @vaciar-carrito = "vaciarCarrito"
    />

    <body>
        
        <main class="container-xl mt-5">
            <h2 class="text-center">Nuestra Colección</h2>

            <div class="row mt-5">

                <Guitarra
                    v-for = "guitarra in guitarras"
                    :guitarra = "guitarra"
                    @agregar-carrito = "agregarCarrito"
                />
                
            
            </div>
        </main>

        
    </body>


    <Footer />

    </template>
