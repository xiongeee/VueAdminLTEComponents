<template>
    <div class="box-body table-responsive">
        <div class="pull-right">
            <div class="box-body">
                <button type="button" class="btn btn-info margin" data-toggle="modal" data-target="#myModal" v-on:click="openModal">{{createGroupLabel}}</button>
                <button type="button" class="btn bg-orange btn-flat margin">Total: {{total}} €</button>
            </div>

        </div>
        <table id="tattoo_table" class="table table-bordered table-striped">
            <thead>
            <tr>
                <th></th>
                <th role="button" v-on:click="orderBy('group_id')">{{groupLabel}}</th>
                <th role="button" v-on:click="orderBy('name')">{{tattooReference}}</th>
                <th role="button" v-on:click="orderBy('price')">{{price}}</th>
                <th role="button" v-on:click="orderBy('feedback')">{{feedback}}</th>
                <th >{{drawing}}</th>
                <th>{{photo}}</th>
                <th role="button" v-on:click="orderBy('client')">{{client}}</th>
                <th role="button" v-on:click="orderBy('date')">{{date}}</th>
                <th>{{action}}</th>
            </tr>
            <tr>
                <th></th>
                <th></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="name" type="text" :placeholder="searchLabel"></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="price" type="text" :placeholder="searchLabel"></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="feedback" type="text" :placeholder="searchLabel"></th>
                <th></th>
                <th></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="client" type="text" :placeholder="searchLabel"></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="date" type="text" :placeholder="searchLabel"></th>
                <th></th>
            </tr>
            </thead>
            <tbody>
            <template v-if="!items">
                <div>
                    <h3>No Records Yet!</h3>
                </div>
            </template>
            <tr v-else v-bind:class="{ info: amIChecked(item.id) }" v-for="(item,index) in items">
                <input type="hidden" :value="item.id">
                <td><input :disabled="item.group" :checked="amIChecked(item.id)" type="checkbox" v-on:click="changeSeleted(item.id,item.price)"></td>
                <td v-if="item.group">{{item.group.name}}</td><td v-else></td>
                <td>{{item.name}}</td>
                <td>{{item.price}}</td>
                <td>{{item.feedback}}</td>
                <td v-if="item.drawing">
                    <a :href="item.drawing" data-toggle="lightbox">
                        <img width="50" :src="item.drawing" class="img-circle">
                    </a>
                </td>
                <td v-else></td>
                <td v-if="item.photo">
                    <a :href="item.photo" data-toggle="lightbox">
                        <img width="50" :src="item.photo" class="img-circle">
                    </a>
                </td>
                <td v-else></td>
                <td>{{item.client}}</td>
                <td>{{item.date}}</td>
                <td>
                    <a role="button" :href="'tattoo/' + item.id + '/edit'"><i class="fa fa-edit"></i></a>
                    <a role="button" v-on:click="removeTattoo(item.id,index)"><i class="fa fa-remove text-red"></i></a>
                </td>
            </tr>
            </tbody>
            <tfoot>
            <tr>
                <th></th>
                <th></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="name" type="text" :placeholder="searchLabel"></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="price" type="text" :placeholder="searchLabel"></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="feedback" type="text" :placeholder="searchLabel"></th>
                <th></th>
                <th></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="client" type="text" :placeholder="searchLabel"></th>
                <th><input v-on:keyup="search" name="searchInputs" v-on:click="cleanInputs" id="date" type="text" :placeholder="searchLabel"></th>
                <th></th>
            </tr>
            <tr>
                <th></th>
                <th role="button" v-on:click="orderBy('group_id')">{{groupLabel}}</th>
                <th role="button" v-on:click="orderBy('name')">{{tattooReference}}</th>
                <th role="button" v-on:click="orderBy('price')">{{price}}</th>
                <th role="button" v-on:click="orderBy('feedback')">{{feedback}}</th>
                <th >{{drawing}}</th>
                <th>{{photo}}</th>
                <th role="button" v-on:click="orderBy('client')">{{client}}</th>
                <th role="button" v-on:click="orderBy('date')">{{date}}</th>
                <th>{{action}}</th>
            </tr>
            </tfoot>
        </table>
        <ul class="pager">
            <li class="previous" v-show="pagination.previous">
                <a role="button" class="page-scroll" v-on:click="fechRecords('previous')">{{previousLabel}}</a>
            </li>
            <li class="next" v-show="pagination.next">
                <a role="button" class="page-scroll" v-on:click="fechRecords('next')">{{nextLabel}}</a>
            </li>
        </ul>
        <div class="modal fade" id="myModal" tabindex="-1" role="dialog">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
                        <h4 class="modal-title">{{modalTitle}}</h4>
                    </div>
                    <div class="modal-body">
                        <div class="box-body">
                            <div class="span6 pull-left" style="text-align:right">
                                <p>{{invoiceAmountLabel}}: {{selected.length}}</p>
                            </div>
                            <div class="span6 pull-right" style="text-align:right">
                                <p>Total: {{total}} €</p>
                            </div>
                            <br><hr>
                            <div class="row">
                                <div class="col-lg-6">
                                    <div class="input-group">
                                        <label>{{dateStartLabel}}</label>
                                        <input class="form-control" type="date" placeholder="dd/mm/yyyy" v-model="groupDateStart">
                                    </div>
                                    <!-- /input-group -->
                                </div>
                                <!-- /.col-lg-6 -->
                                <div class="col-lg-6">
                                    <div class="input-group">
                                        <label>{{dateFinishLabel}}</label>
                                        <input class="form-control" type="date" placeholder="dd/mm/yyyy" v-model="groupDateFinish">
                                    </div>
                                    <!-- /input-group -->
                                </div>
                                <!-- /.col-lg-6 -->
                            </div>
                            <br><hr>
                            <div class="center-block">
                                <input class="form-control" :placeholder="enterGroupName" type="text" v-model="groupName">
                            </div>
                        </div>
                    </div>
                    <alert :can-be-closed=false v-if="messageError" title="Alerta!" :message="messageError"></alert>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-default" data-dismiss="modal" id="modalCancelButton">{{cancelLabel}}</button>
                        <button type="button" class="btn btn-primary" v-on:click="createGroup">{{createGroupLabel}}</button>
                    </div>
                </div><!-- /.modal-content -->
            </div><!-- /.modal-dialog -->
        </div><!-- /.modal -->
    </div>
</template>

<script>
    export default {
        props:{
            groupLabel:{},
            tattooReference:{},
            price:{},
            feedback:{},
            drawing:{},
            photo:{},
            client: {},
            date:{},
            action:{
                default:'Action'
            },
            searchLabel:{},
            previousLabel:{},
            nextLabel:{},
            createGroupLabel:{},
            invoiceAmountLabel:{},
            enterGroupName:{},
            modalTitle:{},
            cancelLabel:{},
            dateStartLabel:{},
            dateFinishLabel:{}
        },
        data: function () {
            return {
                items:null,
                total:0,
                selected:[],
                groupName:'',
                groupDateStart:null,
                groupDateFinish:null,
                modalBody:'',
                messageError:null,
                pagination: {
                    page: 1,
                    previous: false,
                    next: false
                },
                orderByField:'date',
                orderByDir:'DESC',
                orderByFields:{
                    group_id:false,
                    name:false,
                    price:false,
                    feedback:false,
                    client:false,
                    date:false,
                },
                searchTerm:'',
                searchColumn:''
            }
        },
        computed:{
        },
        methods:{
            fechRecords: function (direction) {

                if (direction === 'previous'){
                    --this.pagination.page;
                }
                else if (direction === 'next'){
                    ++this.pagination.page;
                }

                this.$http.get('api/v1/tattoo/'+this.orderByField+'/'+this.orderByDir+'/15/'+this.searchColumn+'/'+this.searchTerm+'?page='+ this.pagination.page).then(function(response){
                    if(response.data.data){
                        this.items =  response.data.data;
                        this.pagination.next = response.data.next_page_url;
                        this.pagination.previous = response.data.prev_page_url;
                    }else console.log(response.data);
                }, function(response){
                    console.log(response);
                });
            },
            createGroup(){
                this.messageError = null;

                if(this.noEnoughData()) return;


                var data = {groupName:this.groupName,
                    ids:this.selected,
                    dateStart:this.groupDateStart,
                    dateFinish:this.groupDateFinish,
                };
                this.$http.post('api/v1/group',data).then(function (response) {
                    if(response.data.ok)
                    {
                        //Reload data
                        this.fechRecords();

                        //Close Modal
                        document.getElementById('modalCancelButton').click();

                        //Redirect to groups
                        window.location = '/group';

                    }else{
                        this.messageError = response.data.message;
                    }
                }, function (response) {
                    this.messageError = response.statusText;
                    console.log(response);
                });
            },
            noEnoughData(){
                if(!this.groupName)
                {
                    this.messageError = 'Rellene el nombre del grupo';
                    return true;

                }else if(this.selected.length <= 0)
                {
                    this.messageError = 'Debe elegir al menos una factura';
                    return true;
                }
            },
            changeSeleted: function (id,price) {
                var index = this.selected.indexOf(id);
                if (index > -1) {
                    this.selected.splice(index, 1);
                    //Sum the new amount
                    this.total -= price;
                }else{
                    this.selected.push(id);
                    this.total += price;
                }

            },
            amIChecked: function (id) {
                if (this.selected.indexOf(id) != -1) return true;
            },
            openModal(){
                this.messageError = null;
            },
            orderBy(field){
                this.orderByField = field;

                if(this.orderByFields[field])
                    this.orderByDir = 'DESC';
                else this.orderByDir = 'ASC';

                this.orderByFields[field] =!this.orderByFields[field];

                //Set pagination to first page
                this.pagination.page = 1;

                this.fechRecords();
            },
            search(event){
                this.searchTerm = event.target.value;
                this.searchColumn = event.target.id;

                //Set pagination to first page
                this.pagination.page = 1;

                this.fechRecords();
            },
            cleanInputs(){
                document.getElementsByName('searchInputs').forEach(function(element) {
                    element.value = '';
                });
                if(0 <= this.items.length){
                    this.searchTerm='';
                    this.fechRecords();
                }
            },
            removeTattoo(id,index)
            {
                var self = this;
                swal({
                    title: 'El tattoo se va a eliminar',
                    text: 'No se podrá recuperar posteriormente',
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonText: 'Si, eliminalo!',
                    cancelButtonText: 'No, dejalo'
                }).then(function() {
                    self.$http.delete('api/v1/tattoo/'+id).then(function (response) {
                        if(response.data.ok)
                        {
                            self.items.splice(index, 1);
                            swal(
                                'Eliminado!',
                                '',
                                'success'
                            )
                        }
                    },function (response) {
                        swal(
                            'No se pudo realizar la operación',
                            response.data.message,
                            'error'
                        )
                        console.log(response.data.message);
                    });
                });
            }
        },
        mounted() {
            console.log('Tattoo Table ready.');
            this.fechRecords();
        }
    }
</script>