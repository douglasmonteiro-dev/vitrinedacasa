<script>
  import { link } from "svelte-routing";
  import axios from "axios";
  import HomeNav from "../components/HomeNav.svelte";
  import Footer from "../components/Footer.svelte";
  import Alert from "../components/Alert.svelte";
  import { userStore } from "../store/User.js";
  import { displayuser } from "../actions/User";

  let user = {
    password: "",
    instagram: "",
  };
  let dp = "img/avatars/avatar.jpg";
  let loading = false;
  async function getPhoto(a) {
    loading = true;
    let u = await displayuser(user.instagram);
    console.log(u);
    if (u) dp = u.dp;
    loading = false;
  }
  let status = -1;
  let mssg = "";
  const login = (e) => {
    e.preventDefault();
    fetch("/api/user/login", {
      method: "POST", // *GET, POST, PUT, DELETE, etc.
      mode: "cors", // no-cors, *cors, same-origin
      cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
      credentials: "same-origin", // include, *same-origin, omit
      headers: {
        "Content-Type": "application/json",
        // 'Content-Type': 'application/x-www-form-urlencoded',
      },
      redirect: "follow", // manual, *follow, error
      referrerPolicy: "no-referrer", // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
      body: JSON.stringify(user),
    })
      .then(function (response) {
        return response.json();
      })
      .then(function (data) {
        console.log(data);
        if (data.status && data.status == 1) {
          status = 1;
          mssg = "Erro";
          return;
        }
        userStore.update((currUser) => {
          return { token: data.token, user: data.user };
        });
        document.location.href = "/dashboard";
      })
      .catch((error) => {
        status = 1;
        mssg = "Tivemos algum problema, por favor tente novamente";
      });
  };
</script>

<HomeNav />
<div class="main d-flex justify-content-center w-100">
  <main class="content d-flex p-0">
    <div class="container d-flex flex-column">
      <div class="row h-100">
        <div class="col-sm-10 col-md-8 col-lg-6 mx-auto d-table h-100">
          <div class="align-middle">
            <div class="text-center mt-4">
              <h1 class="h2">Bem vindo novamente</h1>
              <p class="lead">Entre com sua conta para continuar</p>
            </div>

            <div class="card">
              <div class="card-body">
                <div class="m-sm-4">
                  <div class="text-center">
                    {#if loading}
                      <div class="spinner-border text-primary" role="status">
                        <span class="sr-only">Carregando...</span>
                      </div>
                    {:else}
                      <img
                        src={dp}
                        alt="Not found user"
                        class="img-fluid rounded-circle"
                        width="132"
                        height="132"
                      />
                    {/if}
                  </div>
                  <form on:submit={login}>
                    <div class="form-group">
                      <label for="">Instagram</label>
                      <input
                        on:change={() => {
                          getPhoto(user.instagram);
                        }}
                        class="form-control form-control-lg"
                        type="text"
                        bind:value={user.instagram}
                        placeholder="usuario"
                      />
                    </div>
                    <div class="form-group">
                      <label for="">Senha</label>
                      <input
                        class="form-control form-control-lg"
                        type="password"
                        bind:value={user.password}
                        placeholder="senha"
                      />
                      <small>
                        <a use:link replace href="/resetpassword"
                          >Esqueceu a senha?</a
                        >
                      </small>
                    </div>
                    <div>
                      <div
                        class="custom-control custom-checkbox align-items-center"
                      >
                        <input
                          type="checkbox"
                          class="custom-control-input"
                          value="remember-me"
                          name="remember-me"
                          checked
                        />
                        <label for="" class="custom-control-label text-small"
                          >Lembrar</label
                        >
                      </div>
                    </div>
                    <div class="text-center mt-3">
                      <button class="btn btn-lg btn-primary">Entrar</button>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</div>
<Alert {mssg} {status} />
<Footer />
