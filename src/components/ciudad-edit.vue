<template>
    <div>
        <form @submit.prevent="guardarCambios" novalidate class="needs-validation"
            :class="{ 'was-validated': formSubmitted }">
            <div class="mb-3 d-none">
                <label for="id" class="form-label">ID</label>
                <input type="text" class="form-control" id="id" v-model="ciudadEditada.id" readonly>
            </div>

            <div class="mb-4">
                <label for="nombre" class="form-label fw-bold">Nombre de la Ciudad</label>
                <input type="text" class="form-control" id="nombre" v-model="ciudadEditada.nombre" required>
                <div class="invalid-feedback">El nombre es obligatorio</div>
            </div>

            <div class="mb-4">
                <label for="pais" class="form-label fw-bold">País</label>
                <select class="form-select" id="pais" v-model="ciudadEditada.paisId" required>
                    <option v-for="pais in paises" :key="pais.id" :value="pais.id"
                        :selected="pais.id === ciudadEditada.paisId">
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
                    <i class="bi bi-save me-2"></i>Guardar Cambios
                </button>
            </div>
        </form>
    </div>
</template>

<script>
export default {
    name: 'CiudadEdit',
    props: {
        ciudad: {
            type: Object,
            required: true
        },
        paises: {
            type: Array,
            required: true,
            default: () => []
        }
    },
    data() {
        return {
            ciudadEditada: { ...this.ciudad },
            formSubmitted: false
        }
    },
    watch: {
        ciudad(newVal) {
            this.ciudadEditada = { ...newVal }
        }
    },
    methods: {
        guardarCambios() {
            this.formSubmitted = true

            if (!this.ciudadEditada.nombre || !this.ciudadEditada.paisId) {
                return
            }

            this.$emit('updated', { ...this.ciudadEditada })
        }
    }
}
</script>