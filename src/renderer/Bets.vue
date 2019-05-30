<template>
  <div id="bets" class="content row">
    <div class="col s12">
      <h4>Bet Transaction History - <i class="far fa-question-circle tooltipped" title="Your newest bets will appear after a short time. Please wait."></i>
      
</h4>
      <bet-transaction-list></bet-transaction-list>
    </div>
  </div>
</template>

<script>
import BetTransactionList from '@/components/bets/components/BetTransactionList';

export default {
  name: 'Bets',
  components: { BetTransactionList }
};
</script>
