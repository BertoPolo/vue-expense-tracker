<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDelete" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmit" /><!--  here goes the name of the event you defined inside -->
  </div>
</template>

<script setup>
import Header from "./components/Header.vue"
import Balance from "./components/Balance.vue"
import IncomeExpenses from "./components/IncomeExpenses.vue"
import TransactionList from "./components/TransactionList.vue"
import AddTransaction from "./components/AddTransaction.vue"

import { useToast } from "vue-toastification"
import { ref, computed } from "vue"

const toast = useToast()

const transactions = ref([
  { id: 1, text: "Flowers", amount: -19.99 },
  { id: 2, text: "Salary", amount: 9.1 },
  { id: 3, text: "Book", amount: -19 },
  { id: 4, text: "Camera", amount: 99 },
  { id: 5, text: "Sofa", amount: 150 },
])

// get total amount of expenses
const total = computed(() => transactions.value.reduce((acc, transaction) => acc + transaction.amount, 0))

// get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2)
})
// get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => acc + transaction.amount, 0)
    .toFixed(2)
})

//add transaction
const handleTransactionSubmit = (transactionData) => {
  console.log(transactionData)
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  })
  toast.success("Transaction added")
}

//generate unique id
const generateUniqueId = () => Math.floor(Math.random() * 1000)

//delete transaction
const handleTransactionDelete = (id) => {
  console.log("id to delete: ", id)
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)

  toast.success("Transaction deleted")
}
</script>
