<template>
  <scroll-view
    :content-container-style="{
      contentContainer: {
        paddingVertical: 20,
      },
    }"
  >
    <!-- Content goes here -->

    <status-bar background-color="#002856" />
    <view class="container" v-if="respostaBoolean == false">
      <view>
        <text class="title">Peso do Material:</text>
        <view class="box">
          <text-input
            class="input"
            v-model="dados.pesoMaterial"
            keyboardType="numeric"
          />
          <text style="margin-left: 4; font-size: 25; color: #002856">KG</text>
        </view>

        <text class="title">Relação de Banho 1:</text>
        <view class="box">
          <text-input
            class="input"
            v-model="dados.relacaoBanho"
            keyboardType="numeric"
          />
        </view>
        <text class="title">Adições Futuras:</text>
        <view class="box">
          <text-input
            class="input"
            v-model="dados.adicoesFuturas"
            keyboardType="numeric"
          />
          <text style="margin-left: 4; font-size: 25; color: #002856"
            >Litros</text
          >
        </view>
        <text class="title">Sal/Sulfato:</text>
        <view class="box">
          <text-input
            class="input"
            v-model="dados.salSulfato"
            keyboardType="numeric"
          />
          <text style="margin-left: 4; font-size: 25; color: #002856">g/L</text>
        </view>
        <text class="title">Barrilha:</text>
        <view class="box">
          <text-input
            class="input"
            v-model="dados.barrilha"
            keyboardType="numeric"
          />
          <text style="margin-left: 4; font-size: 25; color: #002856">g/L</text>
        </view>
        <text class="title">Soda Cáustica:</text>
        <view class="box">
          <text-input
            class="input"
            v-model="dados.sodaCaustica"
            keyboardType="numeric"
          />
          <text style="margin-left: 4; font-size: 25; color: #002856">g/L</text>
        </view>
        <text class="title">Densidade Medida:</text>
        <view class="box">
          <text-input
            class="input"
            v-model="dados.densidadeMedida"
            keyboardType="numeric"
          />
          <text style="margin-left: 4; font-size: 25; color: #002856">g/L</text>
        </view>
      </view>
      <view class="alertaField" v-if="erroBranco">
        <text class="alertaText">Preencha todos os campos !</text>
      </view>
      <touchable-opacity class="button" :on-press="() => onPressButton(dados)">
        <text class="buttonText">Calcular</text>
      </touchable-opacity>
    </view>
    <view class="container" v-if="respostaBoolean == true">
      <view class="container--resposta">
        <text class="titleResultado">Acrescentar</text>
        <view class="container--resultado">
          <text class="title">Sal/Sulfato:<text class="info">{{ resposta.sal }}</text> k/gramas </text>
          <text class="title">Barrilha:<text class="info">{{ resposta.barrilha }}</text> Gramas</text>
          <text class="title">Soda:<text class="info">{{ resposta.soda }}</text> Gramas</text>
          <text class="title"
            ><text class="info">{{ resposta.adicionar }}</text> : <text class="info">{{ resposta.solucao }}</text> </text
          >
        </view>
      </view>
      <view class="container--resposta">
        <text class="titleResultado">Informações Adicionais</text>
        <view class="container--resultado">
          <text class="title"
            >Volume Real da Máquina:<text class="info">{{ informacao.volumeRealMaquina }}</text> Litros</text
          >
          <text class="title"
            >Eletrólito da Receita:<text class="info">{{ informacao.eletrolito }} </text> k/gramas</text
          >
          <text class="title"
            >Barrilha da Receita:<text class="info">{{ informacao.barrilhaReceita }}</text> k/gramas</text
          >
          <text class="title"
            >Soda da Receita:<text class="info">{{ informacao.sodaReceita }}</text> k/gramas</text
          >
          <text class="title"
            >Volume/Água a mais ou menos:<text class="info">{{ informacao.volumeAgua }}</text> Litros</text
          >
        </view>
      </view>
      <touchable-opacity class="button" :on-press="() => refazer()">
        <text class="buttonText">Refazer</text>
      </touchable-opacity>
    </view>
  </scroll-view>
</template>
<script>
export default {
  data() {
    return {
      dados: {
        pesoMaterial: "",
        relacaoBanho: "",
        adicoesFuturas: "",
        salSulfato: "",
        barrilha: "",
        sodaCaustica: "",
        densidadeMedida: "",
      },
      resposta: {
        solucao: "",
        soda: "",
        barrilha: "",
        sal: "",
        adicionar: "",
      },
      informacao: {
        volumeRealMaquina: "",
        eletrolito: "",
        barrilhaReceita: "",
        sodaReceita: "",
        volumeAgua: "",
      },
      erroBranco: false,
      respostaBoolean: false,
    };
  },
  methods: {
    onPressButton(dados) {
      if (
        dados.pesoMaterial == "" ||
        dados.relacaoBanho == "" ||
        dados.adicoesFuturas == "" ||
        dados.salSulfato == "" ||
        dados.barrilha == "" ||
        dados.sodaCaustica == "" ||
        dados.densidadeMedida == ""
      ) {
        this.erroBranco = true;
      } else {
        this.erroBranco = false;

        var resposta = new Object();
        var informacao = new Object();

        //calculo volume de banho
        var volumeBanho =
          parseFloat(dados.pesoMaterial) * parseFloat(dados.relacaoBanho);

        //calculo eletrolito
        var eletrolito = volumeBanho * parseFloat(dados.salSulfato);

        //calculo Volume Real da Maquina
        var volumeRealMaquina =
          eletrolito / parseFloat(dados.densidadeMedida) +
          parseFloat(dados.adicoesFuturas);

        //calculo barrilha da receita
        var barrilhaReceita = volumeBanho * parseFloat(dados.barrilha);

        //calculo soda da receita
        var sodaReceita = volumeBanho * parseFloat(dados.sodaCaustica);

        //calculo volume/agua a mais ou menos
        var volumeAgua = volumeRealMaquina - volumeBanho;

        //acrescentar SAL
        var acrescentarSal = volumeAgua * parseFloat(dados.salSulfato);
        if (acrescentarSal > 0) {
          resposta.sal = acrescentarSal;
        } else {
          resposta.sal = 0;
        }

        //acrescentar BARRILHA
        var acrescentarBarrilha = volumeAgua * parseFloat(dados.barrilha);

        if (acrescentarBarrilha > 0) {
          resposta.barrilha = acrescentarBarrilha;
        } else {
          resposta.barrilha = 0;
        }
        //acrescentar SODA
        var acrescentarSoda = volumeAgua * parseFloat(dados.sodaCaustica);
        if (acrescentarSoda > 0) {
          resposta.soda = acrescentarSoda;
        } else {
          resposta.soda = 0;
        }

        if (acrescentarSal < 0) {
          resposta.solucao = -volumeAgua;
        } else {
          resposta.solucao = "ACIMA";
        }

        if (acrescentarBarrilha < 0) {
          resposta.adicionar = "Adicionar Água";
        } else {
          resposta.adicionar = "Adicionar Produto";
        }

        informacao.volumeRealMaquina = volumeRealMaquina;
        informacao.eletrolito = eletrolito;
        informacao.barrilhaReceita = barrilhaReceita;
        informacao.sodaReceita = sodaReceita;
        informacao.volumeAgua = volumeAgua;

        this.resposta = resposta;
        this.informacao = informacao;
        this.respostaBoolean = true;
      }

      /*       this.dados = {
        pesoMaterial: 0,
        relacaoBanho: 0,
        adicoesFuturas: 0,
        salSulfato: 0,
        barrilha: 0,
        sodaCaustica: 0,
        densidadeMedida: 0,
      }; */
    },
    refazer() {
      this.dados = {
        pesoMaterial: "",
        relacaoBanho: "",
        adicoesFuturas: "",
        salSulfato: "",
        barrilha: "",
        sodaCaustica: "",
        densidadeMedida: "",
      };
      this.resposta = {
        solucao: "",
        soda: "",
        barrilha: "",
        sal: "",
        adicionar: "",
      };
      this.informacao = {
        volumeRealMaquina: "",
        eletrolito: "",
        barrilhaReceita: "",
        sodaReceita: "",
        volumeAgua: "",
      };
      this.erroBranco = false;
      this.respostaBoolean = false;
    },
  },
};
</script>

<style>
.container {
  flex: 1;
  flex-direction: column;
  background-color: #fff;
  padding: 20;
  justify-content: space-between;
}
.box {
  flex-direction: row;
  align-items: baseline;
  margin-top: 10;
}
.container--resultado {
  margin-top: 20;
  margin-bottom: 20;
}
.titleResultado {
  padding: 5;
  color: #fff;
  font-size: 35;
  background-color: #002856;
  border-radius: 4;
  text-align: center;
}
.title {
  color: #002856;
  font-size: 20;
}
.button {
  background-color: #002856;
  margin: 20;
  height: 50;
  border-radius: 4;
  justify-content: center;
  align-items: center;
}
.buttonText {
  font-weight: bold;
  font-size: 16;
  color: #fff;
}
.input {
  width: 200;
  text-align: center;
  color: #002856;
  font-size: 20;
  border-width: 4;
  border-color: #002856;
  border-radius: 6;
}
.info{
  font-size: 20;
  font-weight: bold;
  color: #ff0000;
}
.alertaField {
  margin-top: 5;
  padding: 5;
  justify-content: center;
  align-items: center;
  border-width: 4;
  border-color: #ff0000;
  border-radius: 6;
}
.alertaText {
  color: #ff0000;
}
</style>
