<template>
  <form @submit.prevent="guardarCambios">
    <div class="mb-3">
      <label class="form-label">Nombre</label>
      <input type="text" class="form-control" v-model="contactoEditado.nombre" required>
    </div>
    <div class="mb-3">
      <label class="form-label">Teléfono</label>
      <input type="tel" class="form-control" v-model="contactoEditado.telefono" required>
    </div>
    <div class="mb-3">
      <label class="form-label">Correo</label>
      <input type="text" class="form-control" v-model="contactoEditado.correo" required>
    </div>
     <div class="mb-3">
      <label class="form-label">Dirección</label>
      <textarea class="form-control" v-model="contacto.direccion"></textarea>
    </div>

    <div class="modal-footer">
      <button type="button" class="btn btn-secondary" @click="$emit('cancelar')">Cancelar</button>
      <button type="submit" class="btn btn-primary">Guardar Cambios</button>
    </div>
  </form>
</template>

<script>
export default {
  props: {
    contacto: Object,
    paises: Array,
    ciudades: Array
  },
  data() {
    return {
      contactoEditado: { ...this.contacto }
    }
  },
  methods: {
    cambiarPais() {
      this.$emit('pais-seleccionado', this.contactoEditado.paisId)
    },
    guardarCambios() {
      this.$emit('updated', { ...this.contactoEditado })
    }
  }
}
</script>