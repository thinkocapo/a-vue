<template>
  <div id="app">
      <div id="iconsDiv">
        <img id="sentryIcon" alt="Sentry logo" src="./assets/sentry-logo.png" />
        <p class="plus">+</p>
        <img class="icon" alt="Vue logo" src="./assets/logo.png" />
      </div>

      <p id="greeting">{{greetingTxt}}</p>
      <!-- <div id="email-div">
        <input id="emailInput" v-model="userEmail" placeholder="Enter email..." type="email" />
        <button class="event-button" v-on:click="submitEmail">Submit</button>
      </div> -->
      <div id="event-list">
        <EventButton title="TypeError" :onClick="notAFunctionError" />
        <EventButton title="URIError" :onClick="uriError" />
        <EventButton title="SyntaxError" :onClick="syntaxError" />
        <EventButton title="RangeError" :onClick="rangeError" />
        <EventButton title="HTTP Request to Backend" :onClick="restError" />
      </div>
      <div id="product-list">
        <div>
          <ProductSummary :products="products" />
        </div>
      </div>
      <div id="json">
        Pending HTTP Request on page load...
      </div>
      <div id="loadImage-wrap">
        <img id="loadImage" />
        <img id="loadImage2" />
        <img id="loadImage3" />
        <img id="loadImage4" />
        <img id="loadImage5" />
      </div>

    </div>
</template>

<script>
import EventButton from "./components/EventButton.vue";
import ProductSummary from "./components/ProductSummary.vue";
import Vue from "vue";
import * as Sentry from "@sentry/vue";
import { Integrations } from "@sentry/tracing";

const HELLO = "Hello ";

//Required for distributed tracing outside of localhost
const tracingOrigins = ['localhost', 'empowerplant.io', 'run.app', 'appspot.com', /^\//];
const env = "dev";


Sentry.init({
  Vue: Vue,
  dsn: "https://491035397848481eb7291c583b19b930@o87286.ingest.sentry.io/5988614",
  release: process.env.VUE_APP_RELEASE,
  environment: env,
  integrations: [new Integrations.BrowserTracing({
    tracingOrigins: tracingOrigins,
  })],
  tracesSampleRate: 1.0,
});

export default {
  name: "app",
  components: {
    EventButton,
    ProductSummary
  },
  data: function() {
    return { 
      greetingTxt: HELLO, 
      userEmail: "",
      products: [] 
    };
  },
  async created() {
    try {
      var requestOptions = {
        method: 'GET',
        redirect: 'follow'
      };

      fetch("https://application-monitoring-flask-dot-sales-engineering-sf.appspot.com/products", requestOptions)
        .then(response => response.text())
        .then(result => this.products = JSON.parse(result))
        .catch(error => {
          console.log('error', error);
        });
    } catch (ex) {
      console.log(ex);
    }
  },

  methods: {
    loadGraphics: function() {
      if (Math.floor(Math.random() * 10)%2 == 1) {
        document.getElementById("loadImage").src = "https://vastphotos.com/files/uploads/photos/10439/high-resolution-national-park-photo-print-l.jpg";
        document.getElementById("loadImage2").src = "https://vastphotos.com/files/uploads/photos/10312/very-high-resolution-yosemite-valley-sunset-photo-vast-xl.jpg";
        document.getElementById("loadImage3").src = "https://cdn11.bigcommerce.com/s-nq6l4syi/images/stencil/1280x1280/products/25248/266478/83545-1024__88984.1612782086.jpg";
        document.getElementById("loadImage4").src = "https://i.etsystatic.com/9557911/r/il/d31e86/1350658498/il_1588xN.1350658498_b5dc.jpg";
        document.getElementById("loadImage5").src = "https://vastphotos.com/files/uploads/photos/10655/yosemite-el-capitan-vast-xl.jpg";
      }
    },

    setEnvironment: function() {
      if (Math.floor(Math.random() * 10)%3 == 1) {
        this.env = "prod";
      } else if (Math.floor(Math.random() * 10)%3 == 0) {
        this.env = "dev";
      } else {
        this.env = "test";
      }
      console.log("Environment: " + this.env);
    },

    submitEmail: function() {
      Sentry.configureScope(scope => {
        scope.setUser({ email: this.userEmail });
      });
      var newGreeting = HELLO + " " + this.userEmail;
      this.$set(this.$data, "greetingTxt", newGreeting);
    },

    setRandomUser: function() {
      var chars = 'abcdefghijklmnopqrstuvwxyz1234567890';
      var string = '';
      for(var ii=0; ii<15; ii++){
        string += chars[Math.floor(Math.random() * chars.length)];
      }
      let randomEmail = string + "@sentry.io";
      Sentry.configureScope(scope => {
        scope.setUser({ email: randomEmail });
      });

      var newGreeting = HELLO + " " + randomEmail;
      this.$set(this.$data, "greetingTxt", newGreeting);
    },

    notAFunctionError: function() {
      console.log("notAFunctionError...");
      var someArray = [{ func: function() {} }];
      someArray[1].func();
    },
    uriError: function() {
      console.log("uriError...");
      decodeURIComponent("%");
    },

    syntaxError: function() {
      console.log("syntaxError...");
      eval("foo bar");
    },

    rangeError: function() {
      console.log("rangeError...");
      throw new RangeError("Parameter must be between 1 and 100");
    },

    restError: function() {
      const transaction = Sentry.startTransaction({ name: "checkout" });
      // Do this or the trace won't include the backend transaction
      Sentry.getCurrentHub().configureScope(scope => scope.setSpan(transaction));
      //Sentry.configureScope(scope => scope.setSpan(transaction));

      console.log("restError...");
      var myHeaders = new Headers();
      myHeaders.append("Content-Type", "text/plain");

      var raw = "{\"cart\":{\"items\":[{\"id\":4,\"title\":\"Botana Voice\",\"description\":\"Lets plants speak for themselves.\",\"descriptionfull\":\"Now we don't want him to get lonely, so we'll give him a little friend. Let your imagination just wonder around when you're doing these things. Let your imagination be your guide. Nature is so fantastic, enjoy it. Let it make you happy.\",\"price\":175,\"img\":\"https://storage.googleapis.com/application-monitoring/plant-to-text.jpg\",\"imgcropped\":\"https://storage.googleapis.com/application-monitoring/plant-to-text-cropped.jpg\",\"pg_sleep\":\"\",\"reviews\":[{\"id\":4,\"productid\":4,\"rating\":4,\"customerid\":null,\"description\":null,\"created\":\"2021-06-04 00:12:33.553939\",\"pg_sleep\":\"\"},{\"id\":5,\"productid\":4,\"rating\":3,\"customerid\":null,\"description\":null,\"created\":\"2021-06-04 00:12:45.558259\",\"pg_sleep\":\"\"},{\"id\":6,\"productid\":4,\"rating\":2,\"customerid\":null,\"description\":null,\"created\":\"2021-06-04 00:12:50.510322\",\"pg_sleep\":\"\"},{\"id\":13,\"productid\":4,\"rating\":3,\"customerid\":null,\"description\":null,\"created\":\"2021-07-01 00:12:43.312186\",\"pg_sleep\":\"\"},{\"id\":14,\"productid\":4,\"rating\":3,\"customerid\":null,\"description\":null,\"created\":\"2021-07-01 00:12:54.719873\",\"pg_sleep\":\"\"},{\"id\":15,\"productid\":4,\"rating\":3,\"customerid\":null,\"description\":null,\"created\":\"2021-07-01 00:12:57.760686\",\"pg_sleep\":\"\"},{\"id\":16,\"productid\":4,\"rating\":3,\"customerid\":null,\"description\":null,\"created\":\"2021-07-01 00:13:00.140407\",\"pg_sleep\":\"\"},{\"id\":17,\"productid\":4,\"rating\":3,\"customerid\":null,\"description\":null,\"created\":\"2021-07-01 00:13:00.971730\",\"pg_sleep\":\"\"},{\"id\":18,\"productid\":4,\"rating\":3,\"customerid\":null,\"description\":null,\"created\":\"2021-07-01 00:13:01.665798\",\"pg_sleep\":\"\"},{\"id\":19,\"productid\":4,\"rating\":3,\"customerid\":null,\"description\":null,\"created\":\"2021-07-01 00:13:02.278934\",\"pg_sleep\":\"\"}]}],\"quantities\":{\"4\":2},\"total\":350},\"form\":{\"loading\":false}}";
      
      var requestOptions = {
        method: 'POST',
        headers: myHeaders,
        body: raw,
        redirect: 'follow'
      };

      fetch("https://application-monitoring-flask-dot-sales-engineering-sf.appspot.com/checkout", requestOptions)
        .then(function(response) {
          if (!response.ok) {
            const err = new Error(response.status + " -- " + (response.statusText || "Internal Server Error"));
            Sentry.captureException(err);
            console.error(err);
          }
          console.log("transaction.finish");
          transaction.finish();
        });

      console.log("...restError");
      
    },

    getProducts: function() {
      // Do this or the trace won't include the backend transaction
      const transaction = Sentry.getCurrentHub().getScope().getTransaction();
      let span = {};
      if (transaction) {
        span = transaction.startChild({
          op: "http_request",
          description: "load_products",
      })}

      console.log("getProducts...");
      var requestOptions = {
        method: 'GET',
        redirect: 'follow'
      };

      fetch("https://application-monitoring-flask-dot-sales-engineering-sf.appspot.com/products", requestOptions)
        .then(response => response.text())
        .then(result => document.getElementById("json").textContent = result)
        .catch(error => {
          console.log('error', error);
        });


      span.finish();

     
      console.log("...getProducts");
    },

    renderProducts: function() {
      console.log("Render Products");
    }
  },

  /*beforeMount() {
    console.log("beforeMount getProducts()");
    this.getProducts();
  },*/

  mounted() {
    this.setEnvironment();
    this.setRandomUser();
    //this.loadGraphics();
    
    setTimeout(() => {
      this.getProducts();
      //do getProducts call and see slowdown on home page load; print to bottom of buttons to show fetch; don't show /products, just show /
    }, 1);
  }

}

</script>

<style>

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 900px;
  font-weight: bold;

  background-image: url("./assets/sentry-pattern.png");
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

#email-div {
  width: 600px;
  display: inline-flex;
}

#emailInput {
  height: 30px;
  width: 90%;
}

#event-list {
  height: 400px;
  padding: 3rem;
}

#greeting {
  margin-bottom: 0.8rem;
  margin-top: -2rem;
  font-size: 2rem;
}

.icon {
  height: 6rem;
  width: 6rem;
  padding-top: 1.6rem;
  padding-left: 2rem;
}

#sentryIcon {
  height: 9rem;
  width: 9rem;
}

#iconsDiv {
  display: inline-flex;
  padding-bottom: 2rem;
}

.plus {
  font-size: 2.6rem;
}
</style>
