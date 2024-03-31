<template>
  <!-- This is where we include the Header component -->
  <Header/> 
  <!-- This is where we include the Balance component and pass it a prop called total -->
  <Balance :total="+total"/> 
  <!-- This is where we include the IncomeExpenses component -->
  <IncomeExpenses :income="+income" :expenses="+expenses"/>  
  <!-- This is where we include the TransactionList component and pass it a prop called transactions -->
  <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>  
  <!-- This is where we include the AddTransition component -->
  <AddTransition @transactionSubmitted="handleTransactionSubmitted"/>  
</template>

<script setup>
// Importing the components we need from their respective files
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue';
import AddTransition from './components/AddTransition.vue';
import Swal from 'sweetalert2/dist/sweetalert2.js';
import 'sweetalert2/dist/sweetalert2.css';

import {useToast} from 'vue-toastification';
const toast = useToast();

// Importing Vue's ref and computed functions
import { ref, computed, onMounted } from 'vue';

// Creating a variable called transactions and setting its initial value as an array of objects
const transactions = ref ([
]);

onMounted(() => {
  const savedTransactions = localStorage.getItem('transactions');

  // Check if savedTransactions is not null or undefined
  if (savedTransactions) {
    try {
      // Attempt to parse the JSON data
      const parsedTransactions = JSON.parse(savedTransactions);
      
      // Ensure parsedTransactions is an array
      if (Array.isArray(parsedTransactions)) {
        transactions.value = parsedTransactions;
      } else {
        // Handle case where data is not an array
        console.error('Saved transactions data is not an array:', parsedTransactions);
      }
    } catch (error) {
      // Handle parsing error
      console.error('Error parsing saved transactions data:', error);
    }
  }
});

// Creating a computed property called total, which calculates the sum of all transaction amounts
// Defining a computed property called total
const total = computed(() => {
  // Using the computed function to calculate a value based on other reactive properties
  
  // Accessing the current value of the transactions array
  return transactions.value.reduce((acc, transaction) => {
    // Using the reduce method to iterate over each transaction in the transactions array
    // acc: Accumulator (the total sum of amounts so far)
    // transaction: The current transaction being processed
    
    // Adding the amount of the current transaction to the accumulator
    return acc + transaction.amount;
    // The initial value of acc is set to 0 (the second argument of the reduce method)
  }, 0);
  // The reduce method returns the final accumulated value, which represents the total sum of transaction amounts
});

// Get Income

const income = computed(() => {
  // Here we're using the computed function from Vue to create a reactive computed property called 'income'.
  return transactions.value.filter((transaction) => transaction.amount > 0)
  // We're filtering the transactions array to only include transactions where the amount is greater than 0 (positive amounts).
  .reduce((acc, transaction) => {
    // We're using the reduce function to iterate over the filtered transactions and calculate the total income.
    return acc + transaction.amount;
    // We're adding up each transaction amount to the accumulator (acc), which starts at 0.
  }, 0).toFixed(2);
  // We're converting the total income to a fixed decimal notation with 2 decimal places.
});


// Get expenses

const expenses = computed(() => {
  // Here we're using the computed function from Vue to create a reactive computed property called 'income'.
  return transactions.value.filter((transaction) => transaction.amount < 0)
  // We're filtering the transactions array to only include transactions where the amount is less than 0 (negative amounts).
  .reduce((acc, transaction) => {
    // We're using the reduce function to iterate over the filtered transactions and calculate the total income.
    return acc + transaction.amount;
    // We're adding up each transaction amount to the accumulator (acc), which starts at 0.
  }, 0).toFixed(2);
  // We're converting the total income to a fixed decimal notation with 2 decimal places.
});


// Add Transaction

const handleTransactionSubmitted = (transactionData) => {
  // This function is called when a transaction is submitted.

  // We're adding the submitted transaction data to a list of transactions.
  // First, we generate a unique ID for the transaction.
  transactions.value.push({
    id: generateUniqueId(), // Here we generate a unique ID for the transaction.
    text: transactionData.text, // This is the description of the transaction.
    amount: transactionData.amount, // This is how much money is involved in the transaction.
  });

  saveTransactionsToLocalStorage();

  // Then, we show a success message to let the user know the transaction was added.
  toast.success('Transaction Added');

  // Finally, we log the transaction data and the generated unique ID to the console.
  console.log(transactionData); // This shows the details of the transaction in the console.
  console.log(generateUniqueId()); // This shows the unique ID for the transaction in the console.
};


//Generate Unique ID

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

// Delete transaction

const handleTransactionDeleted = (id) => {
  // Show a confirmation dialog using SweetAlert
  Swal.fire({
  title: "Are you sure?",
  text: "You won't be able to revert this!",
  icon: "warning",
  showCancelButton: true,
  confirmButtonColor: "#3085d6",
  cancelButtonColor: "#d33",
  confirmButtonText: "Yes, delete it!",
  customClass: {
    confirmButton: 'custom-style',
    cancelButton: 'custom-style',
    popup: 'custom-style',
    title: 'custom-style',
  }
  }).then((result) => {
    if (result.isConfirmed) {
      // If user confirms, delete the transaction
      transactions.value = transactions.value.filter((transaction) => transaction.id !== id);
      // Show a success message
      saveTransactionsToLocalStorage();
      toast.success('Transaction deleted');
      console.log(id);
    }
  });
};

// Save ro localstorage

const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}
</script>

<style>
/* Increase specificity to override default styles */
.swal2-popup.custom-style {
  border-radius: 10px;
  border: 2px solid #ccc;
  background-color: #fff;
  width: 90%;
  max-width: 400px;
}

/* Other custom styles */
.swal2-title.custom-style {
  color: #000;
  font-size: 20px;
}

/* Add 'custom-style' class to ensure specificity */
.swal2-confirm.custom-style,
.swal2-cancel.custom-style {
  padding: 10px 20px;
  border-radius: 8px;
  font-size: 18px;
}

/* Additional customizations */
.swal2-confirm.custom-style {
  background-color: #b3daff;
  color: #fff;
}

.swal2-cancel.custom-style {
  background-color: #d33;
  color: #fff;
}


</style>
