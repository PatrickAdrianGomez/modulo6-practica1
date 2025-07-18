<template>
  <div class="container mt-4">
    <!-- Encabezado -->
    <div class="d-flex justify-content-between align-items-center mb-4">
      <h1 class="m-0">
        <i class="bi bi-buildings me-2"></i>{{ title }}
      </h1>
      <button class="btn btn-primary" @click="abrirModalParaCrear()">
        <i class="bi bi-plus-circle"></i> Nueva Ciudad
      </button>
    </div>

    <!-- Mensaje si no hay países cargados -->
    <div v-if="!paises || paises.length === 0" class="alert alert-warning">
      <i class="bi bi-exclamation-triangle me-2"></i>
      No hay países disponibles. Debe agregar países primero.
    </div>

    <!-- Tabla de ciudades (solo visible si hay países) -->
    <div v-else class="table-responsive">
      <table class="table table-hover">
        <thead class="table-dark">
          <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>País</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(ciudad, index) in ciudades" :key="ciudad.id">
            <td>{{ ciudad.id }}</td>
            <td>{{ ciudad.nombre }}</td>
            <td>{{ obtenerNombrePais(ciudad.paisId) }}</td>
            <td class="text-nowrap">
              <button class="btn btn-sm btn-outline-warning me-2" @click="abrirModalParaEditar(index)">
                <i class="bi bi-pencil"></i> Editar
              </button>
              <button class="btn btn-sm btn-outline-danger" @click="eliminarCiudad(index)">
                <i class="bi bi-trash"></i> Eliminar
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Modal para edición/creación -->
    <div class="modal fade" id="modalCiudad" tabindex="-1" ref="modalRef">
      <div class="modal-dialog modal-lg">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">
              {{ modalMode === 'crear' ? 'Nueva Ciudad' : 'Editar Ciudad' }}
            </h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
          </div>
          <div class="modal-body">
            <ciudad-add 
              v-if="modalMode === 'crear'" 
              :paises="paises" 
              @created="agregarCiudad"
              @cancelar="cerrarModal"
            />
            <ciudad-edit 
              v-if="modalMode === 'editar' && ciudadSeleccionada" 
              :ciudad="ciudadSeleccionada" 
              :paises="paises"
              @updated="actualizarCiudad"
              @cancelar="cerrarModal"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CiudadAdd from '../components/ciudad-add.vue'
import CiudadEdit from '../components/ciudad-edit.vue'
import paisesList from '../data/paises.js';
import ciudadesList from '../data/ciudades.js';

export default {
  name: 'CiudadList',
  components: {
    CiudadAdd,
    CiudadEdit
  },
  props: {
    paises: {
      type: Array,
      required: true,
      default: () => [] // Valor por defecto como array vacío
    }
  },
  data() {
    return {
      title: 'Listado de Ciudades',
      ciudades: ciudadesList,
      paises: paisesList,
      modalBootstrapInstance: null,
      ciudadSeleccionada: null,
      indiceSeleccionado: null,
      modalMode: 'crear'
    }
  },
  mounted() {
    //this.modalBootstrapInstance = new Modal(this.$refs.modalRef)
    this.initModal()
  },
  methods: {
    initModal() {
      if (this.$refs.modalRef) {
        this.modalBootstrapInstance = new bootstrap.Modal(this.$refs.modalRef);
      } else {
        console.error('Elemento modal no encontrado')
      }
    },
    obtenerNombrePais(paisId) {
      if (!this.paises || !Array.isArray(this.paises)) {
        return 'País no disponible'
      }
      const pais = this.paises.find(p => p.id === paisId)
      return pais ? pais.nombre : 'Desconocido'
    },
    abrirModalParaCrear() {
      if (this.paises.length === 0) {
        alert('Debe agregar países primero')
        return
      }
      if (!this.modalBootstrapInstance) {
        this.initModal() // Intenta reinicializar si es necesario
      }
      this.modalMode = 'crear'
      this.ciudadSeleccionada = null
      this.modalBootstrapInstance?.show() // Uso del operador opcional
    },
    abrirModalParaEditar(index) {
      if (!this.modalBootstrapInstance) {
        this.initModal() // Intenta reinicializar si es necesario
      }
      this.modalMode = 'editar'
      this.indiceSeleccionado = index
      this.ciudadSeleccionada = { ...this.ciudades[index] }
      this.modalBootstrapInstance?.show()
    },
    cerrarModal() {
      this.modalBootstrapInstance?.hide()
    },
    agregarCiudad(nuevaCiudad) {
      const maxId = Math.max(...this.ciudades.map(c => c.id), 0)
      nuevaCiudad.id = maxId + 1
      this.ciudades.push(nuevaCiudad)
      this.cerrarModal()
    },
    actualizarCiudad(ciudadActualizada) {
      this.ciudades.splice(this.indiceSeleccionado, 1, ciudadActualizada)
      this.cerrarModal()
    },
    eliminarCiudad(index) {
      if (confirm('¿Está seguro de eliminar esta ciudad?')) {
        this.ciudades.splice(index, 1)
      }
    }
  }
}
</script>