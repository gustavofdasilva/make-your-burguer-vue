<template>
    <div>
        <Message :message=message v-show="message"/>
    
        <div id="burger-table" v-if="burgers.length>0">
        <div>
            <div id="burger-table-heading">
            <div class="order-id">#:</div>
            <div>Cliente:</div>
            <div>Pão:</div>
            <div>Carne:</div>
            <div>Opcionais:</div>
            <div>Ações:</div>
            </div>
        </div>
        <div id="burger-table-rows">
            <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
            <div class="order-number">{{ burger.id }}</div>
            <div>{{ burger.name }}</div>
            <div>{{ burger.bread }}</div>
            <div>{{ burger.meat }}</div>
            <div v-if="burger.optionals[0]!=''">
                <ul v-for="(opt, index) in burger.optionals" :key="index">
                <li>{{ opt }}</li>
                </ul>
            </div>
            <div v-else style="margin: auto;">
              <p style="font-size: .75em;">Sem opcionais adicionados</p>
            </div>
            <div>
                <select name="status" class="status" @change="updateBurger($event, burger.id)">
                <option :value="s.name" v-for="s in status" :key="s.id" :selected="burger.status == s.name">
                    {{ s.name }}
                </option>
                </select>
                <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
            </div>
            </div>
        </div>
        </div>
        <div v-else>
            <h2>Não há pedidos no momento!</h2>
        </div>
    </div>
  </template>

<script>
import Message from './Message.vue';

    export default {
        name: "Dashboard",
        data () {
            return {
                burgers: [],
                burger_id: null,
                status:null,
                message: null
            }
        },
        components: {
            Message
        },
        mounted() {
            this.getBurgers()
        },
        methods: {
            writeMessage(msg,time) {
                console.log(msg,time)
                this.message = msg
                setTimeout(()=>this.message=null,time)
            },
            async getBurgers() {
                const req = await fetch("http://localhost:8080/burger")
                const data = await req.json()

                this.burgers = data

                console.log(data)
                
                this.getStatus()
            },
            async getStatus() {
                const req = await fetch("http://localhost:8080/status")
                const data = await req.json()

                console.log(data)

                this.status = data
            },
            
            async updateBurger(e, id) {
                const option = e.target.value;

                const dataJson = JSON.stringify({status: option})
                
                const req = await fetch(`http://localhost:8080/burger/${id}`,{
                    method:"PATCH",
                    headers: {"Content-Type":"application/json"},
                    body: dataJson
                })

                const res = await req.json()
                console.log(res)

                this.writeMessage("Pedido atualizado",3000)
                console.log(res)
            },
            async deleteBurger(id) {
                const req = await fetch(`http://localhost:8080/burger/${id}`,{
                    method:"DELETE",
                })

                const res = await req.json()
                console.log(res)

                this.writeMessage("Pedido cancelado",3000)
                this.getBurgers();
            }
        }
    }
</script>

<style scoped>
  #burger-table {
    max-width: 1200px;
    margin: 0 auto;
  }

  #burger-table-heading,
  #burger-table-rows,
  .burger-table-row {
    display: flex;
    flex-wrap: wrap;
  }

  #burger-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
  }

  .burger-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #CCC;
  }

  #burger-table-heading div,
  .burger-table-row div {
    width: 19%;
  }

  #burger-table-heading .order-id,
  .burger-table-row .order-number {
    width: 5%;
  }

  select {
    padding: 12px 6px;
    margin-right: 12px;
    margin-left: -4px;
  }

  .delete-btn {
    background-color: #222;
    color:#fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
    margin-left: -4px;
  }
  
  .delete-btn:hover {
    background-color: transparent;
    color: #222;
  }
  
</style>