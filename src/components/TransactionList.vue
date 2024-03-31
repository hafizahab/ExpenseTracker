<template>
  <h3>History</h3>
  <ul id="list" class="list">
    <li v-for="transaction in transactions" v-bind:key="transaction.id"
      :class="transaction.amount < 0 ? 'minus' : 'plus'"> {{ transaction.text }} <span>RM {{
      transaction.amount }}</span>
      <button class="delete-btn" @click="deleteTransaction(transaction.id)"><i class="fa-solid fa-trash-can"></i></button>
    </li>

  </ul>
</template>

<script setup>

const emit = defineEmits(['transactionDeleted']);

  const props = defineProps({
    transactions: {
      type: Array,
      required: true,
    },
  });

const deleteTransaction = (id) => {
  emit('transactionDeleted', id);
}

</script>
