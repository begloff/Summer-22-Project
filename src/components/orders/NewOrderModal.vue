<template>
    <div class="new-backdrop" @click.self="closeModal">
        <div class="new-modal" @click.self="modal=false, toggleQuantity=false">

            <div style="display: inline">
            <h1 style="display: inline">New Order</h1>
            <button  @click="closeModal" type="button" style="display: inline; float: right " class="btn-close pull-right" aria-label="Close"></button>
            </div>

            <hr>
            <input type="text" class="nameInput" id="nameInput" placeholder="Name (First and Last)" v-model="currentOrder.name" autocomplete="off" @input="filterCustomers" @focus="modal=true">
            <div v-if="filteredCustomers && modal" class="listContainer">
                <ul style="padding-left: 0px;">
                    <li class="listItem" v-for="customer in filteredCustomers" @click="setCustomer(customer)">{{customer}}</li>
                </ul>
            </div>
            <hr>

            <button style="background: green; margin-right: 50px; " @click="addItem('DubBuff')">DubBuff</button>
            <button style="background: red; margin-right: 50px;" @click="addItem('SingleBuff')" >SingleBuff</button>
            <button style="background: #47ad64; margin-right: 50px;" @click="addItem('DubBuff - HS')" >DubBuff - HS</button>
            <button style="background: #9e4f4f; margin-right: 50px;" @click="addItem('SingleBuff - HS')" >SingleBuff - HS</button>
            <button style="margin-right: 50px;" @click="addItem('CBR')" >CBR</button>
            <button style="background: black; margin-right: 50px;" @click="addItem('Single CBR')">Single CBR</button>
            <button style="background: #918e2d; margin-right: 50px;" @click="addItem('Chicken Nachos')">Chicken Nachos</button>
            <button style="background: #2d7691; margin-right: 50px;" @click="addItem('Cheese Nachos')">Cheese Nachos</button>
            <button style="background: #9b5f96; margin-right: 50px;" @click="toggleQuantity = !toggleQuantity">Pizza Rolls</button>
            <button style="background: #571e59; margin-right: 50px;" @click="addItem('Soda/Gatorade')">Soda/Gatorade</button>
            <button style="background: #1e5931; margin-right: 50px;" @click="addItem('Ice Cream Sandwich')">Ice Cream Sandwich</button>

            <hr v-if="toggleQuantity">

            <input type="number" v-if="toggleQuantity" placeholder="Quantity:" @input="addPizzaRolls" v-model="pizzaRolls" @focus="toggleQuantity=true">

            <hr>
            <p style="">Items (click to delete)</p>

            <div v-for="item in currentOrder.items" :key="item" class="pill">
                <span @click="delItem(item)" >{{item}}</span>
            </div>
            <hr>

            <input type="text" placeholder="Comments (optional)" v-model="currentOrder.comments">

            <hr>
            <h2>Ticket Total: ${{Number(currentOrder.price).toFixed(2)}}</h2>

            <hr>
            <label for="cash">Cash Order: </label>
            <input type="checkbox" id="cash" v-model="currentOrder.cash" style="height: 20px;">
            <label for="cash">Hall Staff Order: </label>
            <input type="checkbox" id="cash" v-model="hallStaffOrder" @click="hallStaffCheckBox" style="height: 20px;">

            <hr>
            <button :disabled="(!currentOrder.price && !hallStaffOrder) || !currentOrder.name" @click="submitOrder">Submit Order</button>

            <p v-if="dayOfWeek > 4" style="color: red;">Warning: Atypical Operation Day!</p>

        </div>
    </div>
</template>

<script>

import { ordersCollection, db } from '../../firebase'
import { addDoc, getDoc, doc } from 'firebase/firestore'

export default {

    mounted(){
        this.filterCustomers();
    },

    methods: {

        filterCustomers(){

            if (this.currentOrder.name.length == 0){
                this.filteredCustomers = this.customers
            }

            this.filteredCustomers = this.customers.filter( customer => {
                return customer.toLowerCase().includes(this.currentOrder.name.toLowerCase());
            })
        },

        setCustomer(customer){
            this.currentOrder.name = customer
            this.modal = false
        },

        closeModal(){
            this.$emit('close')
        },

        addItem( item ){

            this.currentOrder.items.push(item)

            if(!this.hallStaffOrder){

                switch(item){
                    case 'DubBuff':
                        this.currentOrder.price += 5
                        break
                    case 'SingleBuff':
                        this.currentOrder.price += 3
                        break
                    case 'DubBuff - HS':
                        this.currentOrder.price += 5
                        break
                    case 'SingleBuff - HS':
                        this.currentOrder.price += 3
                        break
                    case 'CBR':
                        this.currentOrder.price += 5
                        break
                    case 'Cheese Nachos':
                        this.currentOrder.price += 2.50
                        break
                    case 'Chicken Nachos':
                        this.currentOrder.price += 3.50
                        break
                    case 'Single CBR':
                        this.currentOrder.price += 3
                        break
                    case 'Soda/Gatorade':
                        this.currentOrder.price += 1.50
                        break                    
                    case 'Ice Cream Sandwich':
                        this.currentOrder.price += 1.50
                        break

                }
            }
        },

        delItem( item ){

            if(!this.hallStaffOrder){
                switch(item){
                    case 'DubBuff':
                        this.currentOrder.price -= 5
                        break
                    case 'SingleBuff':
                        this.currentOrder.price -= 3
                        break
                    case 'DubBuff - HS':
                        this.currentOrder.price -= 5
                        break
                    case 'SingleBuff - HS':
                        this.currentOrder.price -= 3
                        break
                    case 'CBR':
                        this.currentOrder.price -= 5
                        break
                    case 'Cheese Nachos':
                        this.currentOrder.price -= 2.50
                        break
                    case 'Chicken Nachos':
                        this.currentOrder.price -= 3.50
                        break
                    case 'Single CBR':
                        this.currentOrder.price -= 3
                        break
                    case 'Soda/Gatorade':
                        this.currentOrder.price -= 1.50
                        break                    
                    case 'Ice Cream Sandwich':
                        this.currentOrder.price -= 1.50
                        break
                    default: //Pizza Rolls
                        this.currentOrder.price -= (0.25 * this.pizzaRolls)
                        this.pizzaRolls = 0
                        this.toggleQuantity = 0
                        break
                }
            } else {
                //Still Need to check if pizza rolls were deleted
                let check = item.includes("Pizza Rolls")
                if(check){
                    this.pizzaRolls = 0
                    this.toggleQuantity = 0
                }
            }
            const index = this.currentOrder.items.indexOf(item)
            
            this.currentOrder.items.splice(index,1)
        },

        addPizzaRolls(){

            let match = this.currentOrder.items.find(value => /^([0-9]*) (Pizza Rolls)$/.test(value))

            if(match){ //If there are already Pizza Rolls in the order --> Replace
                const index = this.currentOrder.items.indexOf(match)

                const num = Number(this.currentOrder.items[index].slice(0,-11))
                if(!this.hallStaffOrder){
                    this.currentOrder.price -= (0.25 * num)
                }

                this.currentOrder.items.splice(index,1)
            }
            if(this.pizzaRolls > 0){
                this.currentOrder.items.push(`${this.pizzaRolls} Pizza Rolls`)
                if(!this.hallStaffOrder){
                    this.currentOrder.price += (0.25 * this.pizzaRolls)
                }
            }

        },

        async submitOrder(){

            this.$emit('close')

            const person = await getDoc(doc(db,"customers",this.currentOrder.name))

            if(person.data()){
                this.currentOrder.email = person.data().email
            }

            let d = new Date()

            this.currentOrder.time = d.toLocaleTimeString()
            await addDoc( ordersCollection, this.currentOrder )

        },

        hallStaffCheckBox(){

            //Takes time to model to this.hallStaffOrder so flip the values

            if(!this.hallStaffOrder){
                this.currentOrder.price = 0
            } else {
                for( let i = 0; i < this.currentOrder.items.length; i++ ){
                    switch(this.currentOrder.items[i]){
                        case 'DubBuff':
                            this.currentOrder.price += 5
                            break
                        case 'SingleBuff':
                            this.currentOrder.price += 3
                            break
                        case 'DubBuff - HS':
                            this.currentOrder.price += 5
                            break
                        case 'SingleBuff - HS':
                            this.currentOrder.price += 3
                            break
                        case 'CBR':
                            this.currentOrder.price += 5
                            break
                        case 'Cheese Nachos':
                            this.currentOrder.price += 2.50
                            break
                        case 'Chicken Nachos':
                            this.currentOrder.price += 3.50
                            break
                        case 'Single CBR':
                            this.currentOrder.price += 3
                            break
                        case 'Soda/Gatorade':
                            this.currentOrder.price += 1.50
                            break                    
                        case 'Ice Cream Sandwich':
                            this.currentOrder.price += 1.50
                            break
                        default:
                            this.currentOrder.price += (0.25 * this.pizzaRolls)
                            break;
                    }
                }
            }
        }
    },


    data(){
        return{
            
            currentOrder: {
                name: '',
                items: [],
                comments: '',
                price: 0,
                date: new Date(),
                time: null,
                done: false,
                cash: false,
                online: false,
                email: null,
            },
            dayOfWeek: new Date().getDay(),
            customers: this.$store.state.customerBase,
            filteredCustomers: [],
            modal: false,
            toggleQuantity: false,
            pizzaRolls: null,
            hallStaffOrder: false,

        }
    },
}
</script>

<style>
    button:disabled{
        background-color: grey;
        cursor: not-allowed;
    }
  .new-modal {
    width: auto;
    height: auto;
    padding: 10px;
    margin: 50px 50px;
    max-height: 85%;
    background: rgb(255, 255, 255);
    border-radius: 10px;
    overflow-y: auto;
  }

  .new-backdrop {
    top: 0;
    position: fixed;
    background: rgba(0,0,0,0.5);
    width: 100%;
    height: 100%;
  }

    /* If you are updating style for one specific component need to use .modal */
    .new-modal h1 {
    color: #2c3e50;
    border: none;
    padding: 0;
  }
  
  .new-modal p {
      font-style: italic;
      color: #2c3e50;
  }

  .new-modal .actions {
    text-align: center;
    margin: 30px 0 10px 0;
    color: #333;
  }
  .new-modal .actions a {
    color: #333;
    padding: 8px;
    border: 1px solid #eee;
    border-radius: 4px;
    text-decoration: none;
    margin: 10px;
  }

  .nameInput{
    border: 2px solid lightgray;
  }

  .listContainer{
    max-height: 120px;
    overflow-y: scroll;
    text-align: left;
  }

  .listItem{
    width: 100%; 
    cursor: pointer; 
    border-bottom: solid 1px lightgray;
    padding: 5px;
    background: rgb(232, 233, 233);
    color: black;
  }

  .listItem:hover {
    background: rgb(182, 248, 182);
    font-weight: bold;
  }

  input[type="checkbox"] {
    display: inline-block;
    width: 75px;
    margin: 0 10px 0 0;
    position: relative;
    top: 7px;
  }

</style>