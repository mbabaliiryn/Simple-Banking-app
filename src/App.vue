<template>
  <div class="pt-75 w-50 mx-auto">
    <div class="layout-row align-items-center justify-content-center">
      <input
        type="date"
        class="large outlined"
        data-testid="app-input"
        placeholder="Filter Date"
        v-model="selectedDate"
      />
      <button class="" data-testid="submit-button" @click="filterRecords">
        Filter
      </button>
    </div>
    <div class="card mx-auto table-card mt-50 pb-10">
      <table>
        <thead>
          <tr>
            <th>Date</th>
            <th>Description</th>
            <th>Type</th>
            <th data-testid="amount" class="sortable" @click="sortRecords">
              Amount ($)
            </th>
            <th>Available Balance</th>
          </tr>
        </thead>
        <tbody data-testid="records-body">
          <tr v-for="(txn, index) in filteredTransactions" :key="index">
            <td>{{ txn.date }}</td>
            <td>{{ txn.description }}</td>
            <td>{{ txn.type === 1 ? "Debit" : "Credit" }}</td>
            <td>{{ txn.amount }}</td>
            <td>{{ txn.balance }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: "RecordsTable",
  props: {
    transactions: Array,
  },
  data() {
    return {
      selectedDate: null,
      filteredTransactions: [], // initialize with empty array
      sortDirection: 1, // 1 = ascending, -1 = descending
      sortBy: null,
      sortedTransactions: null,
    };
  },
 created() {
  // Populate the filteredTransactions array with the transactions prop data
  if (this.transactions && this.transactions.length > 0) {
    this.filteredTransactions = this.transactions.map((txn) => {
      const date = new Date(txn.date);
      const year = date.getFullYear();
      const month = (date.getMonth() + 1).toString().padStart(2, "0");
      const day = date.getDate().toString().padStart(2, "0");
      const formattedDate = `${year}-${month}-${day}`;
      const type = txn.type === "credit" ? 0 : 1;
      const amount = parseFloat(txn.amount);
      return {
        ...txn,
        date: formattedDate,
        type: type,
        amount: amount,
      };
    });
  }
},
  methods: {
 filterRecords() {
  if (this.selectedDate && this.transactions && this.transactions.length > 0) {
    this.filteredTransactions = this.transactions.filter(
      (txn) => txn.date === this.selectedDate
    );
  } else {
    this.filteredTransactions = this.transactions;
  }
},
   sortRecords() {
  if (this.filteredTransactions && this.filteredTransactions.length > 0) {
    if (this.sortBy === "amount") {
      this.sortDirection = -this.sortDirection;
    } else {
      this.sortDirection = 1;
      this.sortBy = "amount";
    }
    this.filteredTransactions.sort((a, b) =>
      a.amount > b.amount ? this.sortDirection : -this.sortDirection
    );
  }
},
  }
};
</script>

