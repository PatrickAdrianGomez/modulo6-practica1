<template>
  <form @submit.prevent="guardarContacto">
    <div class="mb-3">
      <label class="form-label">Nombre</label>
      <input type="text" class="form-control" v-model="contacto.nombre" required>
    </div>
    
    <div class="mb-3">
      <label class="form-label">Teléfono</label>
      <input type="tel" class="form-control" v-model="contacto.telefono" required>
    </div>
    
    <div class="mb-3">
      <label class="form-label">Correo</label>
      <input type="email" class="form-control" v-model="contacto.correo" required>
    </div>
    
    <div class="mb-3">
      <label class="form-label">Dirección</label>
      <textarea class="form-control" v-model="contacto.direccion"></textarea>
    </div>
    
    <div class="mb-3">
      <label class="form-label">País</label>
      <select class="form-select" v-model="contacto.paisId" @change="cambiarPais" required>
        <option value="" disabled>Seleccione un país</option>
        <option v-for="pais in paises" :key="pais.id" :value="pais.id">
          {{ pais.nombre }}
        </option>
      </select>
    </div>
    
    <div class="mb-3">
      <label class="form-label">Ciudad</label>
      <select class="form-select" v-model="contacto.ciudadId" :disabled="!contacto.paisId" required>
        <option value="" disabled>Seleccione una ciudad</option>
        <option v-for="ciudad in ciudades" :key="ciudad.id" :value="ciudad.id">
          {{ ciudad.nombre }}
        </option>
      </select>
    </div>
    
    <div class="modal-footer">
      <button type="button" class="btn btn-secondary" @click="$emit('cancelar')">Cancelar</button>
      <button type="submit" class="btn btn-primary">Guardar</button>
    </div>
  </form>
</template>

<script>
export default {
  props: {
    paises: Array,
    ciudades: Array
  },
  data() {
    return {
      contacto: {
        nombre: '',
        telefono: '',
        correo: '',
        direccion: '',
        paisId: null,
        ciudadId: null
      }
    }
  },
  methods: {
    cambiarPais() {
      this.contacto.ciudadId = null
      this.$emit('pais-seleccionado', this.contacto.paisId)
    },
    guardarContacto() {
      this.$emit('created', { ...this.contacto })
    }
  }
}
</script>