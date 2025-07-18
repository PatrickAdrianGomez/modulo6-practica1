<template>
  <div>
    <form @submit.prevent="guardarCiudad" novalidate class="needs-validation" :class="{ 'was-validated': formSubmitted }">
      <div class="mb-4">
        <label for="nombre" class="form-label fw-bold">Nombre de la Ciudad</label>
        <input type="text" class="form-control" id="nombre" v-model="ciudad.nombre" required>
        <div class="invalid-feedback">El nombre es obligatorio</div>
      </div>

      <div class="mb-4">
        <label for="pais" class="form-label fw-bold">País</label>
        <select class="form-select" id="pais" v-model="ciudad.paisId" required>
          <option value="" disabled selected>Seleccione un país</option>
          <option v-for="pais in paises" :key="pais.id" :value="pais.id">
            {{ pais.nombre }} ({{ pais.sigla }})
          </option>
        </select>
        <div class="invalid-feedback">Debe seleccionar un país</div>
      </div>

      <div class="d-flex justify-content-end gap-3 mt-4">
        <button type="button" class="btn btn-outline-secondary" @click="$emit('cancelar')">
          <i class="bi bi-x-circle me-2"></i>Cancelar
        </button>
        <button type="submit" class="btn btn-primary">
          <i class="bi bi-check-circle me-2"></i>Guardar
        </button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: 'CiudadAdd',
  props: {
    paises: {
    type: Array,
    required: true,
    default: () => []
  }
  },
  data() {
    return {
      ciudad: {
        nombre: '',
        paisId: null
      },
      formSubmitted: false
    }
  },
  methods: {
    guardarCiudad() {
      this.formSubmitted = true
      
      if (!this.ciudad.nombre || !this.ciudad.paisId) {
        return
      }

      this.$emit('created', { ...this.ciudad })
      this.resetForm()
    },
    resetForm() {
      this.ciudad = {
        nombre: '',
        paisId: null
      }
      this.formSubmitted = false
    }
  }
}
</script>