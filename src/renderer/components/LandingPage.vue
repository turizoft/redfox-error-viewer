<template>
  <div id="main" class="ui main text container">
    <h1 class="ui header">
      RedFox Log Error messages
    </h1>
    <button class="ui teal button" @click="fetchRows"><i class="refresh icon"></i>Refresh</button>
    <div class="ui divided items">
      <div class="item" v-for="row in rows">
        <div class="ui mini image"><i class="big bug middle aligned icon"></i></div>
        <div class="content">
          <a class="header">{{ row.message }}</a>
          <div class="meta">
            <span>At line: {{ row.extra_line }}</span>
          </div>
          <div class="description">
            <p>At file: {{ row.extra_file }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import SystemInformation from './LandingPage/SystemInformation';
  var mysql = require('mysql');

  export default {
    name: 'landing-page',
    components: { SystemInformation },
    data: function() {
      return {
        rows: []
      }
    },
    methods: {
      fetchRows() {
        var _this = this;
        getRows(function(rows) {
          _this.rows.length = 0;
          rows.forEach(function(row) {
            _this.rows.push(row);
          });
        });
      }
    },
    mounted: function() {
      this.fetchRows();
    }
  }

  function getRows(callback) {
    var connection = mysql.createConnection({
      host: '127.0.0.1',
      user: 'root',
      password: null,
      database: 'redfox_dev'
    });

    connection.connect(function(err) {
      if (err){
        alert("An error ocurred performing the connection.");
      }
    });

    connection.query('SELECT * FROM `CRM_logMessages`  order by timestamp desc LIMIT 15', function(err, rows, fields) {
      if (err) {
        alert("An error ocurred performing the query.");
        return;
      }

      callback(rows);
    });

    connection.end();
  }
</script>

<style lang="scss">
  @import url('https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.2.13/semantic.min.css');

  #main {
    padding-bottom: 48px;
    h1 {
      margin: 32px 0 16px;
    }
    .ui.items {
      
    } 
  }

</style>
