<template>
  <section class="orders-container">
    <header class="orders-tab">
      <ul>
        <li>所有投注</li>
      </ul>
    </header>
    <table class="orders-table">
      <thead>
        <tr>
          <th>时间</th>
          <th>投注者</th>
          <th>奖金</th>
        </tr>
      </thead>
      <tbody>
        <tr 
          :key="index"
          v-for="(order, index) in orders">
          <td>{{dateFormat(order.block_time)}}</td> 
          <td>{{order.action_trace.act.data.result.player}}</td> 
          <td class="payout">
            {{order.action_trace.act.data.result.payout !== '0.0000 EOS' && order.action_trace.act.data.result.payout || ''}}
          </td> 
        </tr>
      </tbody>
    </table>
  </section>
</template>

<script>
  import api from '@/utils/eos';

  export default {
    mounted() {
      // 这里频繁的访问
      setInterval(this.fetchOrders, 5000);
    },

    data() {
      return {
        orders: []
      };
    },

    methods: {
      fetchOrders() {
        api.getActions('fairdicelogs', -1, -20).then(({ actions }) => {
          this.orders = actions.filter(action => {
            return action.action_trace
              && action.action_trace.act
              && action.action_trace.act.data
              && action.action_trace.act.data.result
              && action.action_trace.act.account == "fairdicelogs" 
              && action.action_trace.act.name == "result";
          }).reverse();
        });   
      },

      dateFormat(raw) {
        return new Date(raw+'Z').toLocaleTimeString(); 
      }
    }
  };
</script>

<style scoped>
  .orders-container {
    background-color: #191919;
    padding: 20px;
  }   

  .orders-tab {
    color: #fff;
    display: flex;
    justify-content: center;
    margin-bottom: 20px;
  }

  .orders-tab ul {
    display: flex;
    align-items: center;
    border-bottom: 2px solid #bbb;
  }

  .orders-tab ul li {
    cursor: pointer;
    padding: 7px 35px;
    display: inline-block;
    text-align: center;
    color: #bbb; 
    letter-spacing: .5px;
    font-weight: 600;
  } 

  .orders-table {
    width: 90%;
    color: #fff;
    font-weight: 900;
    letter-spacing: .5px;
    border-collapse: collapse;
    margin: 0 auto;
  }

  .orders-table tbody tr {
    border-radius: 5px;
  }

  .orders-table tbody tr:nth-child(even) {
    background-color: #292929;
  }

  .orders-table td {
    font-size: 12px;
    padding: 20px 0;
    text-align: center;
  }

  .payout {
    color: #02f292;
    text-shadow: 0 0 5px #02f292;
  }
</style>

