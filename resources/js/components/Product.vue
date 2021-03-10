<template>
  <div>
    <h1 class="text-center">Gestionar Productos</h1>
    <hr />
    <button
      @click="
        modify = false;
        openModal();
      "
      type="button"
      class="btn btn-primary"
    >
      Nuevo
    </button>

    <!-- The Modal -->
    <div class="modal fade" :class="{ show: modal }">
      <div class="modal-dialog">
        <div class="modal-content">
          <!-- Modal Header -->
          <div class="modal-header">
            <h4 class="modal-title">{{ tituloModal }}</h4>
            <button
              @click="closeModal()"
              type="button"
              class="close"
              data-dismiss="modal"
            >
              &times;
            </button>
          </div>

          <!-- Modal body -->
          <div class="modal-body">
            <form>
              <div class="form-group">
                <label for="name">Nombre:</label>
                <input
                  v-model="product.name"
                  type="text"
                  class="form-control"
                  placeholder="Introduce el nombre"
                  id="name"
                />
              </div>
              <div class="form-group">
                <label for="description">Descripción:</label>
                <textarea
                v-model="product.description"
                  class="form-control"
                  placeholder="Introduce la descripción"
                  id="description"
                />
              </div>
              <div class="form-group">
                <label for="price">Precio:</label>
                <input
                v-model="product.price"
                  type="number"
                  class="form-control"
                  id="price"
                  min="0.00"
                  max="999999.00"
                />
              </div>
            </form>
          </div>

          <!-- Modal footer -->
          <div class="modal-footer">
            <button
              @click="closeModal()"
              type="button"
              class="btn btn-secondary"
              data-dismiss="modal"
            >
              Cancelar
            </button>
            <button @click="save();" type="button" class="btn btn-success" data-dismiss="modal">
              Guardar
            </button>
          </div>
        </div>
      </div>
    </div>

    <table class="table table-striped">
      <thead class="thead-dark">
        <tr>
          <th scope="col">#</th>
          <th scope="col">Nombre</th>
          <th scope="col">Descripción</th>
          <th scope="col">Precio</th>
          <th scope="col" colspan="2">Acción</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in products" :key="product.id">
          <th scope="row">{{ product.id }}</th>
          <td>{{ product.name }}</td>
          <td>{{ product.description }}</td>
          <td>{{ product.price }}</td>
          <td>
            <button
              @click="
                modify = true;
                openModal(product);
              "
              class="btn btn-warning"
            >
              Editar
            </button>
          </td>
          <td>
            <button @click="destroy(product.id)" class="btn btn-danger">
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
export default {
  data() {
    return {
      product: {
        name: 'yy',
        description: 'yuuu',
        price: 1.02,
      },
      id: 0,
      modify: true,
      modal: 0,
      tituloModal: "",
      products: [],
    };
  },
  methods: {
    async list() {
      const res = await axios.get("products");
      this.products = res.data;
    },

    async destroy(id) {
      const res = await axios.delete("/products/" + id);
      this.list();
    },

    async save() {
      if (this.modify) {
        const res = await axios.put("/products/" + this.id, this.product);
      } else {
        const res = await axios.post("/products", this.product);
      }
      this.closeModal();
      this.list();
    },

    openModal(data={}) {
      this.modal = 1;
      if (this.modify) {
        this.id = data.id;
        this.tituloModal = "Modificar Producto";
        this.product.name = data.name;
        this.product.description = data.description;
        this.product.price = data.price;
      } else {
        this.id = 0;
        this.tituloModal = "Crear Producto";
        this.product.name = '';
        this.product.description = '';
        this.product.price = '';
      }
    },
    closeModal() {
      this.modal = 0;
    },
  },

  created() {
    this.list();
  },
};
</script>
<style>
.show {
  display: list-item;
  opacity: 1;
  background: rgba(0, 0, 0, 0.5);
}
</style>