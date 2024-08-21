<template>
  <div>
    <Message :message=message v-show="message"/>
    <div>
        <form id="burguer-form" @submit="createBurguer($event)">
            <div class="input-container">
                <label for="name">Nome do Cliente</label>
                <input type="text" name="name" id="name" v-model="name" placeholder="Digite seu nome">
            </div>
            <div class="input-container">
                <label for="bread">Escolha seu pão</label>
                <select name="bread" id="bread" v-model="bread">
                    <option value="">Selecione o seu pão</option>
                    <option v-for="bread in breadData" :key=bread.id :value="bread.tipo" >{{ bread.tipo }}</option>
                </select>
            </div>
            <div class="input-container">
                <label for="meat">Escolha sua Carne</label>
                <select name="meat" id="meat" v-model="meat">
                    <option value="">Selecione a sua carne</option>
                    <option v-for="meat in meatData" :key=meat.id :value="meat.tipo">{{ meat.tipo }}</option>
                </select>
            </div>
            <div id="opcionais-container" class="input-container">
                <label id="opcionais-title" for="bread">Selecione os opcionais</label>
                <div class="checkbox-container" v-for="optionalIngredient in optionalData" :key="optionalIngredient.id">
                    <input type="checkbox" name="optional" v-model="optional" :value=optionalIngredient.tipo>
                    <span>{{optionalIngredient.tipo}}</span>
                </div>
            </div>
            <div class="input-container">
                <input type="submit" value="Criar meu Burger" class="submit-btn">
            </div>
        </form>
    </div>
  </div>
</template>

<script>
import Message from './Message.vue';


    export default {
        name: "BurguerForm",
        components: {
            Message
        },
        data() {
            return {
                breadData: null,
                meatData: null,
                optionalData: null,

                message: null,
                
                name: null,
                bread: null,
                meat: null,
                optional: [],
                status: "Solicitado",
                msg: null
            }
        },
        created() {
            this.getIngredients()
        },
        methods: {
            async getIngredients() {
                const req = await fetch('http://localhost:3000/ingredientes')
                const data = await req.json()

                this.breadData = data.paes;
                this.meatData = data.carnes;
                this.optionalData = data.opcionais;
            },
            async createBurguer(e) {
                e.preventDefault()
                const data = {
                    name: this.name,
                    meat: this.meat,
                    bread: this.bread,
                    optional: Array.from(this.optional),
                    status: "Solicitado"
                }

                const dataJson = JSON.stringify(data)
                const req = await fetch("http://localhost:3000/burgers",{
                    method: "POST",
                    headers: {"Content-type":"application/json"},
                    body: dataJson
                })
                
                const res = await req.json()

                this.message = "Pedido realizado com sucesso!"
                setTimeout(()=>{
                    this.message= null
                },3000)

                this.name = ""
                this.bread = ""
                this.meat = ""
                this.optional = ""
            }
        }
    }
</script>

<style scoped>
 #burguer-form {
    max-width: 400px;
    margin: 0 auto;
  }

  .input-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
  }

  label {
    font-weight: bold;
    margin-bottom: 15px;
    color: #222;;
    padding: 5px 10px;
    border-left: 4px solid #fcba03;
  }

  input, select {
    padding: 5px 10px;
    width: 300px;
  }

  #opcionais-container {
    flex-direction: row;
    flex-wrap: wrap;
  }

  #opcionais-title {
    width: 100%;
  }

  .checkbox-container {
    display: flex;
    align-items: flex-start;
    width: 50%;
    margin-bottom: 20px;
  }

  .checkbox-container span,
  .checkbox-container input {
    width: auto;
  }

  .checkbox-container span {
    margin-left: 6px;
    font-weight: bold;
  }

  .submit-btn {
    background-color: #222;
    color:#fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .submit-btn:hover {
    background-color: transparent;
    color: #222;
  }
</style>