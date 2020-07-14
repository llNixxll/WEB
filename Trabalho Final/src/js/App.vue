<template>
  <div class="container">
    <div v-bind:style="{ display: showadd }" id="Adicionar-Func">
      <div class="Titulo">
        <h2>Adicionando novo Pet</h2>
      </div>
      <div class="row Campo">
        <div class="col-3">
          <label>Nome: </label>
        </div>
        <div class="col-9">
          <input v-model="ADD_nome" type="text" placeholder="Nome">
        </div>
      </div>
      <div class="row Campo">
        <div class="col-3">
          <label>Salario: </label>
        </div>
        <div class="col-9">
          <input v-model="ADD_salario" type="text" placeholder="0,00">
        </div>
      </div>
      <div class="row Campo">
        <div class="col-3">
          <label>Idade: </label>
        </div>
        <div class="col-9">
          <input v-model="ADD_idade" type="number" placeholder="0">
        </div>
      </div>
      <div class="row Campo">
        <div class="col-3">
          <label>Foto: </label>
        </div>
        <div class="col-9">
          <input v-model="ADD_imagem" type="text" placeholder="http://url.do.avatar">
        </div>
      </div>
      <div class="row Campo">
        <div class="col-6">
          <button v-on:click.prevent="addFunc">Adicionar</button>
        </div>
        <div class="col-6">
          <button v-on:click.prevent="cancelaADD()">Cancelar</button>
        </div>
      </div>
    </div>
    <div v-bind:style="{ display: showedt }" id="Editar-Func">
      <div class="Titulo">
        <h2>Editando Empregado</h2>
      </div>
      <div class="row Campo">
        <div class="col-3">
          <label>Nome: </label>
        </div>
        <div class="col-9">
          <input v-model="EDT_nome" type="text" placeholder="Nome">
        </div>
      </div>
      <div class="row Campo">
        <div class="col-3">
          <label>Salario: </label>
        </div>
        <div class="col-9">
          <input v-model="EDT_salario" type="text" placeholder="0,00">
        </div>
      </div>
      <div class="row Campo">
        <div class="col-3">
          <label>Idade: </label>
        </div>
        <div class="col-9">
          <input v-model="EDT_idade" type="number" placeholder="0">
        </div>
      </div>
      <div class="row Campo">
        <div class="col-3">
          <label>Foto: </label>
        </div>
        <div class="col-9">
          <input v-model="EDT_imagem" type="text" placeholder="http://url.do.avatar">
        </div>
      </div>
      <div class="row Campo">
        <div class="col-6">
          <button v-on:click.prevent="edtFunc()">Editar</button>
        </div>
        <div class="col-6">
          <button v-on:click.prevent="cancelaEDT()">Cancelar</button>
        </div>
      </div>
    </div>
    <div class="justify-content-lg-center">
      <table class="tabelinha">
        <tr>
          <th>ID</th>
          <th>NOME</th>
          <th>SALARIO</th>
          <th>IDADE</th>
          <th>IMAGEM</th>
          <th>AÇÃO</th>
        </tr>
        <tr v-for="item in items" :key="item.id">
          <td>{{item.id}}</td>
          <td>{{item.employee_name}}</td>
          <td>{{item.employee_salary}}</td>
          <td>{{item.employee_age}}</td>
          <td><img class="imagem" :src="item.profile_image"></td>
          <td><img class="icone" src="./assets/lapis.png" v-on:click.prevent="preparaEdt(item.id)">
          <img class="icone" src="./assets/delete.png" v-on:click.prevent="preparaDelete(item.id)"></td>
        </tr> 
      </table>
    </div>
  </div>
</template>

<script>
import Vue from "vue";

export default Vue.extend({
  data() {
    return {
      itemedt: null,
      showedt: "none",
      showadd: "block",
      items: null,
      ADD_nome: null,
      ADD_salario: null,
      ADD_idade: null,
      ADD_imagem: null,
      EDT_id: null,
      EDT_nome: null,
      EDT_salario: null,
      EDT_idade: null,
      EDT_imagem: null,
      Excluir: false
    };
  },
  methods:{
    addFunc: function(){
      this.$http.post('https://rest-api-employees.jmborges.site/api/v1/create',{
        name: this.ADD_nome,
        salary: this.ADD_salario,
        age: this.ADD_idade,
        profile_image: this.ADD_imagem
      })
        .then(Response => {
          if(Response.status == 200){
            alert("Funcionario adicionado com sucesso!"),
            this.cancelaADD(),
            this.atualizaAi();
          }
        });
    },
    preparaEdt: function(id){
      this.showedt = "block";
      this.showadd = "none";
      this.$http.get('https://rest-api-employees.jmborges.site/api/v1/employee/'+id)
        .then(Response => {
          this.EDT_id = Response.body.data.id,
          this.EDT_nome = Response.body.data.employee_name,
          this.EDT_salario = Response.body.data.employee_salary,
          this.EDT_idade = Response.body.data.employee_age,
          this.EDT_imagem = Response.body.data.profile_image
        });


    },
    edtFunc: function(){
      this.$http.put('http://rest-api-employees.jmborges.site/api/v1/update/'+this.EDT_id,{
        name: this.EDT_nome,
        salary: this.EDT_salario,
        age: this.EDT_idade,  
        profile_image: this.EDT_imagem
      })
        .then(
          Response => {
            if(Response.status == 200){
              alert("Editado com sucesso!"),
              this.cancelaEDT(),
              this.atualizaAi();
            }
          },
          this.showedt = "none",
          this.showadd = "block"
          );
    },
    preparaDelete: function(id){
      this.Excluir = confirm("Deseja Excluir o ID: "+id+" ?");
      if (this.Excluir == true) {
        this.deleteFunc(id);
      };
    },
    deleteFunc: function(id){
      this.$http.delete('http://rest-api-employees.jmborges.site/api/v1/delete/'+id)
        .then(Response => {
          if(Response.status == 200){
            alert("Funcionario deletado com sucesso!"),
            this.atualizaAi();
          }
        });
    },
    cancelaEDT: function(){
      this.EDT_id = null,
      this.EDT_nome = null,
      this.EDT_salario = null,
      this.EDT_idade = null,
      this.EDT_imagem = null,
      this.showedt = "none",
      this.showadd = "block"
    },
    cancelaADD: function(){
      this.ADD_nome = null,
      this.ADD_imagem = null,
      this.ADD_salario = null,
      this.ADD_idade = null
    },
    atualizaAi: function(){
      this.$http.get('https://rest-api-employees.jmborges.site/api/v1/employees')
      .then(Response => this.items = Response.data.data);
    }
  },
  mounted: function(){
    this.atualizaAi();
  }
});
</script>

<style lang="scss" scoped>
.container {
  color: green;
}
</style>