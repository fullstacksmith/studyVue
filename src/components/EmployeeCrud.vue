<template>
    <div class="p-4">
        <h2 class="text-xl font-bold mb-4">Product Management</h2>

        <!-- Form -->
        <form @submit.prevent="isEditing? updateProduct() : createProduct()" class="space-y-2">
            <input v-model="form.title" type="text" placeholder="Title" class="border p-2 w-full" required />
            <input v-model="form.description" type="text" placeholder="Description" class="border p-2 w-full" required />
            <input v-model="form.category" type="text" placeholder="Category" class="border p-2 w-full" required />
            <input v-model="form.price" type="number" placeholder="Price" class="border p-2 w-full" required />
            <input v-model="form.stock" type="number" placeholder="Stock" class="border p-2 w-full" required />

            <button class="bg-blue-600 text-white p-2 rounded">
                {{ isEditing ? 'Update Product' : 'Create Product' }}
            </button>
            <button v-if="isEditing" @click="cancelEdit" type="button" class="bg-gray-400 text-white p-2 rounded">
                Cancel
            </button>
        </form>

        <!-- Product List -->
         <ul class="mt-6 space-y-2">
            <li v-for="Product in Products" :key="Product.id" class="flex justify-between p-2 border">
                <div>
                    <span>Title: {{ Product.title }} 
                        <br/> Description: {{ Product.description }} 
                        <br/> Category: {{ Product.category }} 
                        <br/> Price: {{ Product.price }} 
                        <br/> Stock: {{ Product.stock }}</span>
                </div>          
                <div>
                    <button @click="editProduct(Product)" class="bg-yellow-500 text-white p-1 rounded mr-2">Edit</button>
                    <button @click="deleteProduct(Product.id)" class="bg-red-500 text-white p-1 rounded">Delete</button>
                </div>
            </li>
        </ul>
    </div>
</template>


<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const Products = ref([]);
const form = ref({
    email: '',
    salary: '',
    age: ''
});
const isEditing = ref(false);
const editingId = ref(null);

// Fetch Products from the API
async function fetchProducts() {
    try {
        const response = await axios.get('https://dummyjson.com/products');
        Products.value = response.data.products || []; // Ensure Products is an array
    } catch (error) {
        console.error('Error fetching Products:', error);
    }
}
// Create a new Product
async function createProduct() {
    try {
        const response = await axios.post('https://dummyjson.com/products/add', form.value);
        Products.value.push(response.data);
        resetForm();
    } catch (error) {
        console.error('Error creating Product:', error);
    }
}

// Update an existing Product
async function updateProduct() {
    try {
        const response = await axios.put(`https://dummyjson.com/products/${editingId.value}`, form.value);
        const index = Products.value.findIndex(emp => emp.id === editingId.value);
        if (index !== -1) {
            Products.value[index] = response.data;
        }
        resetForm();
    } catch (error) {
        console.error('Error updating Product:', error);
    }
}
// Delete an Product
async function deleteProduct(id) { 
    try {
        await axios.delete(`https://dummyjson.com/products/${id}`);
        // Remove the Product from the local array        
        Products.value = Products.value.filter(emp => emp.id !== id);
    } catch (error) {
        console.error('Error deleting Product:', error);
    }
}   

// Edit an Product
function editProduct(Product) {       
    form.value.title = Product.title;
    form.value.description = Product.description;
    form.value.category = Product.category;
    form.value.price = Product.price;
    form.value.stock = Product.stock;
    editingId.value = Product.id;
    isEditing.value = true;
}
// Reset form
function resetForm() {  
    form.value.title = '';
    form.value.description = '';
    form.value.category = '';
    form.value.price = '';
    form.value.stock = '';
    isEditing.value = false;
    editingId.value = null;
}   

// Cancel edit
function cancelEdit() {
    resetForm();
}

onMounted(() => {
    fetchProducts();
});

</script>