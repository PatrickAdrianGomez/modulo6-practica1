<template>
    <div class="container mt-4">
        <!-- Encabezado con título y botón -->
        <div class="d-flex justify-content-between align-items-center mb-4">
            <h1>{{ title }}</h1>
            <button class="btn btn-primary" @click="abrirModalParaCrear()">
                <i class="bi bi-plus-circle"></i> Nuevo País
            </button>
        </div>

        <!-- Tabla -->
        <div class="table-responsive">
            <table class="table table-hover table-bordered border-light">
                <thead class="table-dark">
                    <tr>
                        <th>ID</th>
                        <th>Nombre</th>
                        <th>Sigla</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in getPaises" :key="item.id">
                        <td>{{ item.id }}</td>
                        <td>{{ item.nombre }}</td>
                        <td>{{ item.sigla }}</td>
                        <td class="text-nowrap">
                            <button class="btn btn-sm btn-outline-warning me-2" @click="abrirModalParaEditar(index)">
                                <i class="bi bi-pencil"></i> Editar
                            </button>
                            <button class="btn btn-sm btn-outline-danger" @click="eliminarPais(index)">
                                <i class="bi bi-trash"></i> Eliminar
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="modalAutoEditar" tabindex="-1" ref="modalRef">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title">
                            {{ modalMode === 'crear' ? 'Nuevo País' : 'Editar País' }}
                        </h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                    </div>
                    <div class="modal-body">
                        <paises-add v-if="modalMode === 'crear'" @created="agregarPais($event)" />
                        <paises-edit v-if="modalMode === 'editar' && paisSeleccionado" :pais="paisSeleccionado" @update="guardarEdicion" />
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import PaisesAdd from '../components/paises-add.vue';
import PaisesEdit from '../components/paises-edit.vue';
import paisesList from '../data/paises.js';

export default {
    name: 'PaisesList',
    data() {  // Corregido de date() a data()
        return {
            title: 'Lista de Países',
            items: paisesList,
            modalBootstrapInstance: null,
            paisSeleccionado: null,
            indiceSeleccionado: 0,
            modalMode: "crear"
        }
    },
    components: {
        PaisesAdd,
        PaisesEdit
    },
    mounted() {
        this.$nextTick(() => {
            if (this.$refs.modalRef) {
                this.modalBootstrapInstance = new bootstrap.Modal(this.$refs.modalRef);
            } else {
                console.error('No se encontró el ref modalRef');
            }
        });
    },
    methods: {
        abrirModalParaCrear() {
            this.modalMode = "crear";
            this.paisSeleccionado = null;
            if (this.modalBootstrapInstance) {
                this.modalBootstrapInstance.show();
            }
        },
        abrirModalParaEditar(index) {
            this.modalMode = "editar";
            this.indiceSeleccionado = index;
            this.paisSeleccionado = { ...this.items[index] };
            
            this.$nextTick(() => {
                if (this.modalBootstrapInstance) {
                    this.modalBootstrapInstance.show();
                }
            });
        },
        cerrarModal() {
            if (this.modalBootstrapInstance) {
                this.modalBootstrapInstance.hide();
            }
        },
        guardarEdicion(paisEditado) {
            console.log(paisEditado);
            this.items[this.indiceSeleccionado] = paisEditado;
            this.cerrarModal();
        },
        eliminarPais(index) {
            if (confirm('¿Estás seguro de que deseas eliminar este país?')) {
                this.items.splice(index, 1);
            }
        },
        agregarPais(nuevoPais) {
            const maxId = Math.max(...this.items.map(item => item.id));
            nuevoPais.id = maxId + 1;
            this.items.push(nuevoPais);
            this.cerrarModal();
        }
    },
    computed: {
        getPaises() {
            if (this.paisSeleccionado && this.paisSeleccionado.nombre) {
                return this.items.filter(item => 
                    item.nombre.includes(this.paisSeleccionado.nombre)
                );
            }
            return [...this.items];
        }
    }
}
</script>