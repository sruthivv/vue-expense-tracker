<template>
  <Header />
  <Balance :total="total" />
  <IncomeExpense :income="+income" :expenses="-expenses" />
  <TransactionList :transactions="transactions" @transactionDeleted="handleDeleteTransaction"/>
  <add-transaction 
  @transactionSubmitted="handleTransactionSubmitted" />
</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpense from './components/IncomeExpense.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'

import { ref, computed, onMounted } from 'vue'
import { useToast } from 'vue-toastification'

const toast = useToast()

  const transactions = ref([])
  // Get Total
  const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
  })

  //Get Income  
  const income = computed(() => {
    return transactions.value.filter((transaction) => transaction.amount > 0).reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2);
  })

  //Get expences  
  const expenses = computed(() => {
    return transactions.value.filter((transaction) => transaction.amount < 0).reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2);
  })

  //add transactions 
  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: generateUniqueID(),
      text: transactionData.text,
      amount: transactionData.amount 
    })

    saveToLocalStorage();
    toast.success('Transaction added')
  }

  //generate unique id
  const generateUniqueID = () => {
    return Math.floor(Math.random() * 1000000)
  }

  //delete transaction
  const handleDeleteTransaction = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

    saveToLocalStorage();
    toast.success('Transaction deleted successfully')
  } 

  //Save to localstorage
  const saveToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
  }

  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

    if(savedTransactions) {
      transactions.value = savedTransactions;
    }
  })
</script>

