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
import { ref, computed, onMounted } from "vue"

const toast = useToast()
onMounted(() => {
  //this is something like useEffect
  let savedTransactions = JSON.parse(localStorage.getItem("transactions"))

  if (savedTransactions) transactions.value = savedTransactions
})

const transactions = ref([])

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
  saveTransactionsToLocalStorage()
  toast.success("Transaction added")
}

//generate unique id
const generateUniqueId = () => Math.floor(Math.random() * 1000)

//delete transaction
const handleTransactionDelete = (id) => {
  console.log("id to delete: ", id)
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
  saveTransactionsToLocalStorage()
  toast.success("Transaction deleted")
}

//save to local storage
const saveTransactionsToLocalStorage = () => {
  //this part i do not understand at all
  localStorage.setItem("transactions", JSON.stringify(transactions.value))
}
</script>
