<template>
    <div class="container mt-3">
        <!-- Encabezado con ícono -->
        <div class="d-flex align-items-center mb-4">
            <i class="bi bi-globe-americas fs-3 me-2 text-primary"></i>
            <h4 class="m-0 text-primary">{{ title }}</h4>
        </div>

        <!-- Formulario con mejor estructura -->
        <form @submit.prevent="submit" novalidate class="needs-validation" :class="{ 'was-validated': formSubmitted }">
            <!-- Campo Nombre -->
            <div class="mb-4">
                <label for="nombre" class="form-label fw-bold">Nombre del País</label>
                <div class="input-group">
                    <span class="input-group-text">
                        <i class="bi bi-fonts"></i>
                    </span>
                    <input type="text" class="form-control" id="nombre" v-model="pais.nombre"
                        :class="{ 'is-invalid': v$.pais.nombre.$error }" 
                        placeholder="Ej: Argentina" required>
                </div>
                <div class="invalid-feedback d-block" v-if="v$.pais.nombre.$error">
                    {{ v$.pais.nombre.$errors[0].$message }}
                </div>
                <small class="form-text text-muted">Nombre completo del país</small>
            </div>

            <!-- Campo Sigla -->
            <div class="mb-4">
                <label for="sigla" class="form-label fw-bold">Código de País</label>
                <div class="input-group">
                    <span class="input-group-text">
                        <i class="bi bi-code-square"></i>
                    </span>
                    <input type="text" class="form-control text-uppercase" id="sigla" v-model="pais.sigla"
                        :class="{ 'is-invalid': v$.pais.sigla.$error }" 
                        placeholder="Ej: AR" maxlength="3" required>
                </div>
                <div class="invalid-feedback d-block" v-if="v$.pais.sigla.$error">
                    {{ v$.pais.sigla.$errors[0].$message }}
                </div>
                <small class="form-text text-muted">Código de 2-3 letras (ISO Alpha-2/3)</small>
            </div>

            <!-- Botones con mejor espaciado -->
            <div class="d-flex justify-content-between mt-5">
                <button type="button" class="btn btn-outline-secondary px-4" 
                    @click="$emit('cancelar')" data-bs-dismiss="modal">
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
// Importing necessary libraries
import { reactive } from 'vue';
import useVuelidate from '@vuelidate/core';
import { required, maxLength, minLength } from '@vuelidate/validators';

export default {
    name: 'PaisesAdd',
    data() {
        return {
            title: 'Agregar Pais',
            pais: reactive({
                nombre: '',
                sigla: ''
            }),
            v$: null
        }
    },
    components: {
        // Register any components if needed
    },
    created() {
        const rules = {
            pais: {
                nombre: { required, minLength: minLength(3), maxLength: maxLength(50) },
                sigla: { required, minLength: minLength(2), maxLength: maxLength(3) }
            }
        }
        this.v$ = useVuelidate(rules, { pais: this.pais });
    },
    methods: {
        async submit() {
            const isValid = await this.v$.$validate();
            if (isValid) {
                this.$emit('created', { ...this.pais });
                this.pais.nombre = '';
                this.pais.sigla = '';
            } else {
                console.error('Formulario no válido');
            }
        }
    },
    computed: {},
    props: {}
}
</script>