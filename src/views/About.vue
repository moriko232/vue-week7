<template>
  <div class="about">
    <Header></Header>
    <div class="main-content">
      <div class="container">
        <div class="row align-items-start justify-content-center">
          <div class="col-md-6">
            <h2>關於皂皂</h2>
            <img
              src="https://images.unsplash.com/photo-1591977025723-bdd717522fc7?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1350&q=80"
              alt=""
            />
            <div class="mt-3">
              <p>
                用最好最天然的配方呵護您的肌膚，清爽不乾澀，無論哪種季節都能找到適合您膚質的手工皂，搭配恰到好處的極致香氛，為生活增添一點迷人優雅。
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Header from "@/components/_shared/Header.vue";

export default {
  name: "About",
  components: {
    Header
  }
};
</script>
