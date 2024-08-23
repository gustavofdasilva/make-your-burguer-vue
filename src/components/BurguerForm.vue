<template>
  <div>
    <Message :message=message :theme=messageTheme v-show="message"/>
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
                    <option v-for="bread in breadData" :key=bread.id :value="bread.name" >{{ bread.name }}</option>
                </select>
            </div>
            <div class="input-container">
                <label for="meat">Escolha sua Carne</label>
                <select name="meat" id="meat" v-model="meat">
                    <option value="">Selecione a sua carne</option>
                    <option v-for="meat in meatData" :key=meat.id :value="meat.name">{{ meat.name }}</option>
                </select>
            </div>
            <div id="opcionais-container" class="input-container">
                <label id="opcionais-title" for="bread">Selecione os opcionais</label>
                <div class="checkbox-container" v-for="optionalIngredient in optionalData" :key="optionalIngredient.id">
                    <input type="checkbox" name="optional" v-model="optional" :value=optionalIngredient.name>
                    <span>{{optionalIngredient.name}}</span>
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
                messageTheme: null,
                
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
            writeMessage(msg,time,theme) {
                  console.log(msg,time)
                  this.messageTheme = theme
                  this.message = msg
                  setTimeout(()=>this.message=null,time)
            },
            async getIngredients() {
                const req = await fetch('http://localhost:8080/ingredients')
                const data = await req.json()

                console.log(data)
                this.breadData = data.breads;
                this.meatData = data.meats;
                this.optionalData = data.optionals;
            },
            async createBurguer(e) {
                e.preventDefault()

                if(!this.name) {
                  this.writeMessage("Digite seu nome!",3000,"warn")
                  return
                }
                
                if (!this.meat || !this.bread ) {
                  this.writeMessage("Selecione ao menos um tipo de carne e um tipo de pão!",3000,"warn")
                  return
                }

                const data = {
                    name: this.name,
                    meat: this.meat,
                    bread: this.bread,
                    optionals: Array.from(this.optional),
                    status: "Solicitado"
                }
                  
                console.log(data)

                const dataJson = JSON.stringify(data)
                const req = await fetch("http://localhost:8080/burger",{
                    method: "POST",
                    headers: {"Content-type":"application/json"},
                    body: dataJson
                })
                
                const res = await req.json()

                this.writeMessage("Pedido realizado com sucesso!",3000,"success")

                this.name = null
                this.bread = null
                this.meat = null
                this.optional = []
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