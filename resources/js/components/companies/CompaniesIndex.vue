<template>
    <div>
        <div class="form-group">
            <router-link :to="{name: 'createCompany'}" class="btn btn-success">Create new company</router-link>
        </div>

        <div class="panel panel-default">
            <div class="panel-heading">Companies list
                <div class="col-md-8 pull-right">
                    <label class="col-md-2">Search name:</label>
                    <input class="col-md-4" type="text" v-model="searchText" @keyup="search()">
                </div>
            </div>

        </div>
        <div class="panel-body">
            <table class="table table-bordered table-striped">
                <thead>
                <tr>
                    <th>Name</th>
                    <th>Address</th>
                    <th>Website</th>
                    <th>Email</th>
                    <th width="100">&nbsp;</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="company, index in companies">
                    <td>{{ company.name }}</td>
                    <td>{{ company.address }}</td>
                    <td>{{ company.website }}</td>
                    <td>{{ company.email }}</td>
                    <td>
                        <router-link :to="{name: 'editCompany', params: {id: company.id}}"
                                     class="btn btn-xs btn-default">
                            Edit
                        </router-link>
                        <a href="#"
                           class="btn btn-xs btn-danger"
                           v-on:click="deleteEntry(company.id, index)">
                            Delete
                        </a>
                    </td>
                </tr>
                </tbody>
            </table>
        </div>
        <div class="panel-footer">
            <paginate
                v-model="currentPage"
                :page-count="pageCount"
                :container-class="'pagination'"
                :prev-text="'prev'"
                :next-text="'next'"
                :click-handler="clickCallback">
            </paginate>
        </div>
    </div>
    </div>
</template>

<script>
    export default {
        data: function () {
            return {
                currentPage: 1,
                companies: [],
                pageCount: 0,
                searchText: '',
            }
        },
        mounted() {
            var app = this;
            axios.get('/api/v1/companies')
                .then(function (resp) {
                    app.pageCount = resp.data.total;
                    app.companies = resp.data.data;
                })
                .catch(function (resp) {
                    console.log(resp);
                    alert("Could not load companies");
                });
        },
        methods: {
            deleteEntry(id, index) {
                if (confirm("Do you really want to delete it?")) {
                    var app = this;
                    axios.delete('/api/v1/companies/' + id)
                        .then(function (resp) {
                            app.companies.splice(index, 1);
                        })
                        .catch(function (resp) {
                            alert("Could not delete company");
                        });
                }
            },
            clickCallback(e) {
                console.log(e);
            },
            search() {
                axios.post('/api/v1/search', {
                    name: this.searchText
                })
                    .then((response) => {
                        this.companies = response.data;
                    });
            }
        }
    }
</script>

