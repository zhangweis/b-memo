<template>
  <div class="hello">
<div v-if='loading'>loading...</div>
    <div v-for="memo in memos">
{{memo.t  | moment("YYYY-MM-DD HH:mm")}}
<hr/>
<div v-html="memo.html"></div> <a v-bind:href="'https://www.bitpaste.app/tx/'+memo.tx">...</a> 

    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import * as MarkdownIt from 'markdown-it';
const _MarkdownIt: any = MarkdownIt;

const md = new _MarkdownIt();

@Component
export default class HelloWorld extends Vue {
  memo:any = {};
  memos:Array<any> = [];
  loading= false;
  async mounted() {
    var posterId = location.hash.substring(1);
    this.loading = true;
    this.memos = await this.getPosts(posterId);
		this.memos.filter((memo)=>memo.content).forEach((memo)=>{
			console.log(memo.content);
			memo.html = md.render(memo.content);
		});
    this.loading = false;
	}
	async getPosts(addr:string) {
var query = {
  "v": 3,
  "q": {
    "find": {
"out.s1":"19HxigV4QyBv3tHpQVcUEQyq1pzZVdoAut",
"out.s7":"15PciHG22SNLQJXMoSUaWVi7WSqc7hCfva",
"out.s9":addr
    },
    "limit": 10
  },
  "r": {
    "f": "[.[] | { content: .out[0].ls2, content1: .out[0].s2, s1: .out[0].s1, s3: .out[0].s3, tx:.tx.h, t: .blk.t} ]"
  }
}
var b64 = btoa(JSON.stringify(query));
var url = "https://genesis.bitdb.network/q/1FnauZ9aUH2Bex6JzdcV4eNX7oLSSEbxtN/" + b64;

var header = {
    headers: { key: "174MzsMvzRZymoojUPq5UqPxrpdwZhHBy9" }
};
console.log(JSON.stringify(query));
var r = await (await fetch(url, header)).json();
var txs = r.u.concat(r.c);

var types = ['text/markdown'];
txs = txs.filter((tx:any)=>types.indexOf(tx.s3)>=0).filter((tx:any)=>tx.content||tx.content1);
return txs.map((tx:any)=>{return {
  tx: tx.tx,
  content: tx.content||tx.content1,
  t: tx.t
};});

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
