<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Banyaknya USER {{ jum }}
                        <button @click="showModal()" class="btn btn-primary btn-sm float-right">
                            + User
                        </button>
                    </div>

                    <div class="card-body">
                        <table class="table table-bordered">
                            <thead>
                                <th>NO</th>
                                <th>Nama</th>
                                <th>Email</th>
                                <th></th>
                            </thead>
                            <tbody>
                                <tr v-for="(user, index) in users" :key="index">
                                    <td>{{ index+1 }}</td>
                                    <td>{{ user.name }}</td>
                                    <td>{{ user.email }}</td>
                                    <td>
                                        <button @click="update(user)" class="btn btn-info btn-sm">Edit</button>
                                        <button @click="Hapus(user)" class="btn btn-danger btn-sm">Hapus</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="modal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel"
        aria-hidden="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
            <div class="modal-header text-center">
                <h4 class="modal-title w-100 font-weight-bold" >Pendaftar baru</h4>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <div class="modal-body mx-3">
                <div class="md-form mb-5">
                <i class="fas fa-user prefix grey-text"></i>
                <input type="text" id="orangeForm-name" class="form-control validate" v-model="userr.name">
                <label data-error="wrong" data-success="right" for="orangeForm-name" >Your name</label>
                </div>
                <div class="md-form mb-5">
                <i class="fas fa-envelope prefix grey-text"></i>
                <input type="email" id="orangeForm-email" class="form-control validate" v-model="userr.email">
                <label data-error="wrong" data-success="right" for="orangeForm-email">Your email</label>
                </div>

                <div class="md-form mb-4">
                <i class="fas fa-lock prefix grey-text"></i>
                <input type="password" id="orangeForm-pass" class="form-control validate" v-model="userr.pass">
                <label data-error="wrong" data-success="right" for="orangeForm-pass">Your password</label>
                </div>
            </div>
            <div class="modal-footer d-flex justify-content-center">
                <button class="btn btn-deep-orange" @click="simpan">Simpan</button>
                <button class="btn btn-deep-orange" @click="resetModal(), hidden()">Batal</button>
            </div>
            </div>
        </div>
        </div>

    </div>

    
</template>

<script>
    export default {
        data(){
            return{
                users:[],
                userr:{
                    id:'',
                    name:'',
                    email:'',
                    pass:''
                },
                jum:'',
                edit: false,
                error:''
            }
        },
        methods:{
            showModal(){
                $("#modal").modal("show");
            },
            hidden()
            {
                $('#modal').modal("hide");
            },
            GetUsers()
            {
                axios.get('api/users')
                .then(response => {
                    this.users = response.data.datanya
                    this.jum = Object.keys(this.users).length
                })
            },
            Hapus(user)
            {
                 let decision = confirm('Yakin menghapus nama = '+ user.name);
                 if(decision ===  true){
                    axios.delete('api/del/'+ user.id)
                    .then(response=>{
                        this.GetUsers()
                    })
                 }
            },
            simpan()
            {
                if(this.edit === false)
                { 
                    axios.post('api/simpan', {
                     name: this.userr.name,
                     email: this.userr.email,
                     pass : this.userr.pass
                    })
                    .then(response =>{
                        $("#modal").modal("hide");
                        this.GetUsers();
                        this.resetModal();
                    })
                    .catch(function (error) {
                        //
                    })
                }else{
                    axios.put('api/edit/'+ this.userr.id,{
                        name: this.userr.name,
                        email:this.userr.email,
                    })
                    .then(response =>{
                        this.edit = false;
                        this.hidden();
                        this.GetUsers();
                        this.resetModal();
                    })
                }
            },
            resetModal()
            {
                this.userr.name = '';
                this.userr.email = '';
                this.userr.pass = '';
                this.edit = false;
            },
            update(user)
            {
                //console.log(user.id);
                this.userr.id = user.id;
                this.userr.name = user.name;
                this.userr.email = user.email;
                this.userr.pass =  user.pass;

                this.edit = true;

                this.showModal();
            },
        },
        mounted() {
            this.GetUsers();
        }
    }
</script>
