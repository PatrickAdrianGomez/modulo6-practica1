<template>
  <div class="container mt-4">
    <!-- Encabezado con ícono -->
    <div class="d-flex align-items-center mb-4">
      <i class="bi bi-pencil-square fs-4 me-2 text-primary"></i>
      <h4 class="m-0 text-primary">{{ title }}</h4>
    </div>

    <!-- Formulario mejorado -->
    <form @submit.prevent="emitirDatos" novalidate class="needs-validation">
      <!-- Campo ID (oculto) -->
      <div class="mb-3 d-none">
        <label for="id" class="form-label">ID</label>
        <input type="text" class="form-control" id="id" v-model="editado.id" readonly>
      </div>

      <!-- Campo Nombre -->
      <div class="mb-4">
        <label for="nombre" class="form-label fw-bold">Nombre del País</label>
        <div class="input-group has-validation">
          <span class="input-group-text">
            <i class="bi bi-globe-americas"></i>
          </span>
          <input type="text" class="form-control" id="nombre" 
                 v-model="editado.nombre" required
                 placeholder="Ingrese el nombre del país">
          <div class="invalid-feedback">
            Por favor ingrese el nombre del país
          </div>
        </div>
      </div>

      <!-- Campo Sigla -->
      <div class="mb-4">
        <label for="sigla" class="form-label fw-bold">Código del País</label>
        <div class="input-group has-validation">
          <span class="input-group-text">
            <i class="bi bi-upc-scan"></i>
          </span>
          <input type="text" class="form-control text-uppercase" id="sigla" 
                 v-model="editado.sigla" required maxlength="3"
                 placeholder="Ej: AR, BR, CL"
                 @input="editado.sigla = $event.target.value.toUpperCase()">
          <div class="invalid-feedback">
            Por favor ingrese el código del país (2-3 letras)
          </div>
        </div>
        <small class="form-text text-muted">Código de 2-3 letras (ej: AR, BR, CL)</small>
      </div>

      <!-- Botones mejorados -->
      <div class="d-flex justify-content-end gap-3 mt-4">
        <button type="button" class="btn btn-outline-secondary px-4" 
                @click="$emit('cancelar')">
          <i class="bi bi-x-circle me-2"></i>Cancelar
        </button>
        <button type="submit" class="btn btn-primary px-4">
          <i class="bi bi-check-circle me-2"></i>Guardar
        </button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: 'PaisesEdit',
  props: {
    pais: {
      type: Object,
      required: true,
      default: () => ({ id: null, nombre: '', sigla: '' })
    },
    title: {
      type: String,
      default: 'Editar País'
    }
  },
  data() {
    return {
      editado: { ...this.pais } // Copia profunda del objeto para edición
    }
  },
  methods: {
    emitirDatos() {
      // Validación básica del formulario
      const form = this.$el.querySelector('form')
      if (!form.checkValidity()) {
        form.classList.add('was-validated')
        return
      }

      // Emitir los datos editados
      this.$emit('update', { ...this.editado })
    }
  },
  watch: {
    // Actualizar los datos editados cuando cambia la prop
    pais: {
      handler(newVal) {
        this.editado = { ...newVal }
      },
      deep: true
    }
  }
}
</script>

<style scoped>
.form-label {
  color: #495057;
  margin-bottom: 0.5rem;
}

.input-group-text {
  background-color: #f8f9fa;
  min-width: 42px;
  justify-content: center;
}

.btn {
  min-width: 120px;
}

/* Efecto hover para botones */
.btn-outline-secondary:hover {
  background-color: #f8f9fa;
}

/* Validación */
.was-validated .form-control:invalid {
  border-color: #dc3545;
}

.was-validated .form-control:valid {
  border-color: #28a745;
}
</style>