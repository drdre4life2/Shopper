<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Products</div>
                   <form @submit.prevent="addProduct" class="mb-3">
  <div class="form-group">
    <input type="text" class="form-control" id="exampleInputEmail1" placeholder="Name" v-model="product.name">
  </div>
   <div class="form-group">
    <textarea type="text" class="form-control"  placeholder="Detail" v-model="product.detail"> </textarea>
  </div>
  
  <button type="submit" class="btn btn-primary">Save</button>
</form>
                    <div class="card-body">
                        <nav aria-label="Page navigation example">
                           <ul class="pagination">
                            <li v-bind:class="[{disabled: !pagination.prev_page_url}]"
                             class="page-item" ><a class="page-link" href="#"
                            @click ="fetchProducts(pagination.prev_page_url)" >Previous</a></li>
   
                           <li class="page-item disabled"><a class="page-link text-dark" href="#">Page {{pagination.current_page}} of {{pagination.last_page}}</a></li>
                          <li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item"><a class="page-link" href="#"
                          @click ="fetchProducts(pagination.next_page_url)">Next</a></li>
                        </ul>
                   </nav>
                       <div v-for="product in products" v-bind:key ="product.id">
                           <h3>{{product.name}}</h3>
                           <h6>{{product.detail}}</h6>
                           <hr>
                            <button @click="editProduct(product)" class="btn  btn-large btn-danger mb-3" >Edit</button>
<hr>
                        <button @click="deleteProduct(product.id)" class="btn btn-large btn-danger">Delete</button>
                       </div>
                        
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return{
                products:[],
                product: {
                id:'',
                name: '',
                detail:''
                },
                product_id: '',
                pagination: {},
                edit: false
            }
        },
        created(){
            this.fetchProducts();

        },
        methods: {
            fetchProducts(page_url) {
                let vm = this
                 page_url = page_url || '/api/products' 
                fetch(page_url)
                .then( res =>res.json())
                .then(res =>  {
                 this.products = res.data;
                 vm.makePagination(res.meta, res.links);
                })
                .catch(err => console.log(err))
            },
            makePagination(meta, links){
                let pagination = {
                    current_page: meta.current_page,
                    last_page: meta.last_page,
                    next_page_url: links.next,
                    prev_page_url: links.prev

                }
                this.pagination = pagination;
            },
            deleteProduct(id){
                if(confirm('Are you sure?')){
                   // alert(id);
                    fetch('api/products/${id}',{
                        method: 'delete'
                    })
                    .then(res => res.json())
                    .then(data => { alert('Product Removed');
                    this.fetchProducts();
                    })
                    
                    .catch(err => console.log(err));
                }
            },
            addProduct(){
            if (this.edit == false){
                // add product
                fetch('api/products',{ 
                    method: 'post',
                    body: JSON.stringify(this.product),
                    headers: {
                        'content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.product.name = '';
                    this.product.detail = '';
                    alert('Product added')
                    this.fetchProducts;
                })
                .catch(err => console.log(err));
            }else{


                   fetch('api/products/',{ 
                    method: 'put',
                    body: JSON.stringify(this.product),
                    headers: {
                        'content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.product.name = '';
                    this.product.detail = '';
                    alert('Product edited')
                    this.fetchProducts;
                })
                .catch(err => console.log(err));
            }
            },
             editProduct(product){
                 this.edit = 'true';
                 this.product.id = product.id;
                 this.product.product_id = product.id;
                 this.product.detail = product.detail;
                 this.product.name = product.name;


             }
        }
    }
</script>
