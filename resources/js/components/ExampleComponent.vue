<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">
                        <div class="card-tools">
                            <button class="btn btn-sm btn-primary" @click="newModal">Make Transaction</button>
                        </div>
                    </div>

                    <div class="card-body">
                        <p>Balance: Ksh. {{this.balance}}</p>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="addnew" tabindex="-1" role="dialog" aria-labelledby="addnewLabel"
             aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="addnewLabel">Transaction</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <form @submit.prevent="makeTransaction()">
                        <div class="modal-body">
                            <div class="form-group">
                                <label for="type">Type</label>
                                <select v-model="form.type" class="form-control" name="type" id="type"
                                        :class="{ 'is-invalid': form.errors.has('type') }">
                                    <option selected value="">--Select Type--</option>
                                    <option value="1">Deposit</option>
                                    <option value="2">Withdraw</option>
                                </select>
                                <has-error :form="form" field="type"></has-error>
                            </div>
                            <div class="form-group">
                                <label>Amount</label>
                                <input v-model="form.amount" type="number" name="amount"
                                       placeholder="Enter Amount"
                                       class="form-control" :class="{ 'is-invalid': form.errors.has('amount') }">
                                <has-error :form="form" field="amount"></has-error>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                            <button type="submit" class="btn btn-success">Complete Transaction</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                balance: '',
                form: new Form({
                    type: '',
                    amount: '',
                })
            }
        },
        methods: {
            makeTransaction(){
                this.form.post('api/transact')
                    .then(() => {
                        Fire.$emit('entry');
                        toast.fire({
                            type: 'success',
                            title: 'The transaction was successful'
                        });
                        this.form.reset();
                        $('#addnew').modal('hide');
                    })
                    .catch(error => {
                        this.errors = error.response.data.errors;
                        swal.fire({
                            type: 'error',
                            title: 'Error!!',
                            text: error.response.data.message,

                        })
                    })
            },
            newModal() {
                this.form.reset();
                $('#addnew').modal('show');
            },
            getBalance() {
                axios.get("/api/transact").then(({data}) => ([this.balance = data]));
            }
        },
        created() {
            this.getBalance();
            Fire.$on('entry', () =>{
                this.getBalance();
            })
        }
    }
</script>
