<template>
    <div class="container mt-4">
        <!-- Encabezado con buscador -->
        <div class="d-flex justify-content-between align-items-center mb-4 flex-wrap">
            <h1 class="mb-3 mb-md-0">
                <i class="bi bi-person-lines-fill me-2"></i>{{ title }}
            </h1>

            <div class="d-flex flex-column flex-md-row gap-3 w-100 w-md-auto">
                <!-- Buscador -->
                <div class="search-box position-relative flex-grow-1">
                    <i class="bi bi-search position-absolute"></i>
                    <input type="text" class="form-control ps-4" v-model="filtroNombre"
                        placeholder="Buscar por nombre..." @input="aplicarFiltro">
                    <button v-if="filtroNombre" class="btn btn-sm btn-link position-absolute end-0 top-0 bottom-0"
                        @click="limpiarFiltro">
                        <i class="bi bi-x"></i>
                    </button>
                </div>

                <!-- Botón Nuevo Contacto -->
                <button class="btn btn-primary" @click="abrirModalParaCrear()">
                    <i class="bi bi-plus-circle me-2"></i> Nuevo
                </button>
            </div>
        </div>

        <!-- Mensaje cuando no hay resultados -->
        <div v-if="contactosFiltrados.length === 0" class="alert alert-info">
            <i class="bi bi-info-circle me-2"></i>
            {{ filtroNombre ? 'No se encontraron contactos con ese nombre' : 'No hay contactos registrados' }}
        </div>

        <!-- Tabla de contactos -->
        <div v-else class="table-responsive">
            <table class="table table-hover">
                <thead class="table-dark">
                    <tr>
                        <th>ID</th>
                        <th>Nombre</th>
                        <th>Teléfono</th>
                        <th>Correo</th>
                        <th>País</th>
                        <th>Ciudad</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(contacto, index) in contactosFiltrados" :key="contacto.id">
                        <td>{{ contacto.id }}</td>
                        <td>{{ contacto.nombre }}</td>
                        <td>{{ contacto.telefono }}</td>
                        <td>{{ contacto.correo }}</td>
                        <td>{{ obtenerNombrePais(contacto.paisId) }}</td>
                        <td>{{ obtenerNombreCiudad(contacto.ciudadId) }}</td>
                        <td>
                            <button class="btn btn-sm btn-outline-warning me-2" @click="abrirModalParaEditar(index)">
                                <i class="bi bi-pencil"></i>
                            </button>
                            <button class="btn btn-sm btn-outline-danger" @click="eliminarContacto(index)">
                                <i class="bi bi-trash"></i>
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="modalContacto" tabindex="-1" ref="modalRef">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title">
                            {{ modalMode === 'crear' ? 'Nuevo Contacto' : 'Editar Contacto' }}
                        </h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                    </div>
                    <div class="modal-body">
                        <contacto-add v-if="modalMode === 'crear'" :paises="paises" :ciudades="ciudadesFiltradas"
                            @created="agregarContacto" @cancelar="cerrarModal"
                            @pais-seleccionado="actualizarCiudadesFiltradas" />
                        <contacto-edit v-if="modalMode === 'editar' && contactoSeleccionado"
                            :contacto="contactoSeleccionado" :paises="paises" :ciudades="ciudadesFiltradas"
                            @updated="actualizarContacto" @cancelar="cerrarModal"
                            @pais-seleccionado="actualizarCiudadesFiltradas" />
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import ContactoAdd from '@/components/contacto-add.vue'
import ContactoEdit from '@/components/contacto-edit.vue'
import paisesData from '@/data/paises.js'
import ciudadesData from '@/data/ciudades.js'

export default {
    components: { ContactoAdd, ContactoEdit },
    data() {
        return {
            contactos: [
                { id: 1, nombre: "Patrick Gómez", telefono: "72696012", correo: "patrick_adrian@hotmail.com", direccion: "Calle 123", paisId: 9, ciudadId: 9 },
                { id: 2, nombre: "Ana Gómez", telefono: "555-5678", correo: "ana@example.com", direccion: "Av. Principal", paisId: 2, ciudadId: 3 }
            ],
            paises: paisesData,
            title: 'Lista de Contactos',
            ciudades: ciudadesData,
            ciudadesFiltradas: [],
            modalBootstrapInstance: null,
            contactoSeleccionado: null,
            indiceSeleccionado: null,
            modalMode: 'crear',
            filtroNombre: '',
            contactosFiltrados: []
        }
    },
    mounted() {
        //this.modalBootstrapInstance = new bootstrap.Modal(this.$refs.modalRef)
        this.initModal()
    },
    created() {
        this.contactosFiltrados = [...this.contactos]
    },
    methods: {
        initModal() {
            if (this.$refs.modalRef) {
                this.modalBootstrapInstance = new bootstrap.Modal(this.$refs.modalRef);
            } else {
                console.error('Elemento modal no encontrado')
            }
        },
        aplicarFiltro() {
            if (!this.filtroNombre) {
                this.contactosFiltrados = [...this.contactos]
                return
            }

            const termino = this.filtroNombre.toLowerCase()
            this.contactosFiltrados = this.contactos.filter(contacto =>
                contacto.nombre.toLowerCase().includes(termino)
            )
        },
        limpiarFiltro() {
            this.filtroNombre = ''
            this.contactosFiltrados = [...this.contactos]
        },
        obtenerNombrePais(paisId) {
            const pais = this.paises.find(p => p.id === paisId)
            return pais ? pais.nombre : 'Desconocido'
        },
        obtenerNombreCiudad(ciudadId) {
            const ciudad = this.ciudades.find(c => c.id === ciudadId)
            return ciudad ? ciudad.nombre : 'Desconocida'
        },
        actualizarCiudadesFiltradas(paisId) {
            this.ciudadesFiltradas = this.ciudades.filter(c => c.paisId === paisId)
        },
        abrirModalParaCrear() {
            if (!this.modalBootstrapInstance) {
                this.initModal() // Intenta reinicializar si es necesario
            }
            this.modalMode = 'crear'
            this.contactoSeleccionado = null
            this.ciudadesFiltradas = []
            this.modalBootstrapInstance.show()
        },
        abrirModalParaEditar(index) {
            if (!this.modalBootstrapInstance) {
                this.initModal() // Intenta reinicializar si es necesario
            }
            const contactoFiltrado = this.contactosFiltrados[index]
            const indiceReal = this.contactos.findIndex(c => c.id === contactoFiltrado.id)
            this.modalMode = 'editar'
            this.indiceSeleccionado = index
            this.contactoSeleccionado = { ...this.contactos[indiceReal] }
            this.actualizarCiudadesFiltradas(this.contactoSeleccionado.paisId)
            this.modalBootstrapInstance.show()
        },
        agregarContacto(nuevoContacto) {
            const maxId = Math.max(...this.contactos.map(c => c.id), 0)
            nuevoContacto.id = maxId + 1
            this.contactos.push(nuevoContacto)
            this.aplicarFiltro() // Actualizar la lista filtrada
            this.cerrarModal()
        },
        actualizarContacto(contactoActualizado) {
            this.contactos.splice(this.indiceSeleccionado, 1, contactoActualizado)
            this.aplicarFiltro() // Actualizar la lista filtrada
            this.cerrarModal()
        },
        eliminarContacto(index) {
            const contactoFiltrado = this.contactosFiltrados[index]
            const indiceReal = this.contactos.findIndex(c => c.id === contactoFiltrado.id)

            if (confirm('¿Eliminar este contacto?')) {
                this.contactos.splice(indiceReal, 1)
                this.aplicarFiltro() // Reaplicar filtro después de eliminar
            }
        },
        cerrarModal() {
            this.modalBootstrapInstance.hide()
        }
    }
}
</script>