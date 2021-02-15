<template>
  <div class="veiculo">
      <h2>Veiculos</h2>

      <div class="form">
        <p> <span>Veículo: </span> </p>
        <p> <input v-model="veiculo"> </p>

        <p> <span>Marca: </span> </p>
        <p> <input v-model="marca"> </p>

        <p> <span>Ano: </span> </p>
        <p> <input v-model="ano"> </p>

        <p><span>Descrição: </span> </p>
        <p> <textarea v-model="descricao" rows="5" cols="50"></textarea> </p>

        <p>
              <input type="radio" id="verdadeiro" v-model="vendido" value="true">
              <label for="verdadeiro">Vendido   </label> 

              <input type="radio" id="falso" v-model="vendido" value="false">
              <label for="falso">Não Vendido</label>
        </p>

        <button v-on:click="enviar">Enviar</button>
      </div>
      <br /><br />

      <div class="tabela">
        <table id="tabelaVeiculo" class="tabelaVeiculo" style="width: 100%; font-size: 20px;">
          <thead>
            <tr>
              <th scope="col">#</th>
              <th scope="col">Veículo</th>
              <th scope="col">Marca</th>
              <th scope="col">Descrição</th>
              <th scope="col">Ano</th>
              <th scope="col">Vendido</th>
            </tr>
          </thead>
          <tbody id="bodyTabela">
            <tr v-for="item in listaVeiculos" :key="item.id">
              <th scope="row">{{ item.id }}</th>
              <td>{{ item.veiculo }}</td>
              <td>{{ item.marca }}</td>
              <td>{{ item.descricao }}</td>
              <td>{{ item.ano }}</td>
              <td>{{ item.vendido }}</td>
              <td>
                <button v-on:click="editar(item)" class="btn btn-info">Editar</button>
                <button v-on:click="excluir(item)" class="btn btn-danger">Excluir</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <br/><br/>
      <p class="find">
        <button v-on:click="exibirNaoVendidos">Veiculos Não vendidos</button>
        <button v-on:click="exbirAgrupadoPorFabricante">Agrupados por Fabricante</button>
        <button v-on:click="exbirVeiculosSemana">Veículos vendidos na semana</button>
      </p>
     
  </div>
</template>

<script>

export default {
  name: 'Veiculo',
  data () {
    return {
        id: '',
        veiculo: '',
        marca: '',
        ano: '',
        descricao: '',
        vendido: '',
        listaVeiculos: [],
        listIteracao: [],
        index: ''
    }
  },
  methods: {
        enviar: function(){
          console.log(this.id)
          if(this.id == ''){
            this.cadastrar()
          }
          else{
            this.atualizar()
          }
        },
        cadastrar: function () {
          const request  = { veiculo: this.veiculo,  
                             marca: this.marca, 
                             ano: this.ano, 
                             descricao: this.descricao, 
                             vendido: this.vendido };

          this.axios.post("http://localhost:8080/veiculos", request)
          .then(response => 
            console.log(response),
            alert("Cadastro enviado"),
            this.listarTodos()
          )
      },
      atualizar: function () {
          const request  = { veiculo: this.veiculo,  
                             marca: this.marca, 
                             ano: this.ano, 
                             descricao: this.descricao, 
                             vendido: this.vendido };

          this.axios.put("http://localhost:8080/veiculos/" + this.id, request)
          .then(response => 
            console.log(response),
            alert("Atualização enviada"),
            this.listarTodos()
          )
      },
      listarTodos: function () {
        this.axios.get("http://localhost:8080/veiculos")
        .then(response => this.listaVeiculos = response.data)
      },
      editar: function (item) {
        this.id = item.id
        this.veiculo = item.veiculo
        this.marca = item.marca
        this.descricao = item.descricao
        this.ano = item.ano
        this.vendido = item.vendido
      },
      excluir: function (item) {
        this.id = item.id
        this.axios.delete("http://localhost:8080/veiculos/" + this.id)
        .then(response => 
          console.log(response),
          alert("Exlcusão enviada"))
      },
      exibirNaoVendidos: function(){
        this.axios.get("http://localhost:8080/veiculos/find?q=nvendido")
        .then(response => alert("Quantidade de Veículos não vendidos: " + response.data))
      },
      exbirAgrupadoPorFabricante: function(){
        this.axios.get("http://localhost:8080/veiculos/find?q=fabricante")
        .then(response => 
          this.iterar(response)
        )
      },
      exbirVeiculosSemana: function(){
        this.axios.get("http://localhost:8080/veiculos/find?q=semana")
        .then(response => 
          this.iterarSemana(response)
        )
      },
      iterar: function(response){
      this.listIteracao = response.data
        var texto = ''
        for(this.item in this.listIteracao){
          texto += this.listIteracao[this.item] + '\n'
        }
        alert(texto)
      },
      iterarSemana: function(response){
      this.listIteracao = response.data
        var texto = ''
        for(this.item in this.listIteracao){
          console.log(this.listIteracao[this.item] )
          texto += "Veículo: " + this.listIteracao[this.item].veiculo + " / Marca: "+ this.listIteracao[this.item].marca  + " / Ano: "+ this.listIteracao[this.item].ano +'\n'
        }
        alert(texto)
      }
  },
  created: function () {
    this.listarTodos()
  }
}
</script>


<style>

</style>
