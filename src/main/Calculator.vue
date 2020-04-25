<template>
  <div class="calculator">
      <Display :value="displayValue" />
      <Button label="AC" triple @onClick="clearMemory" />
      <Button label="/" operation @onClick="setOperation" />
      <Button label="7" @onClick="addDigit" />
      <Button label="8" @onClick="addDigit" />
      <Button label="9" @onClick="addDigit" />
      <Button label="*" operation @onClick="setOperation" />
      <Button label="4" @onClick="addDigit" />
      <Button label="5" @onClick="addDigit" />
      <Button label="6" @onClick="addDigit" />
      <Button label="-" operation @onClick="setOperation" />
      <Button label="1" @onClick="addDigit" />
      <Button label="2" @onClick="addDigit" />
      <Button label="3" @onClick="addDigit" />
      <Button label="+" operation @onClick="setOperation" />
      <Button label="0" @onClick="addDigit" double />
      <Button label="." @onClick="addDigit" />
      <Button label="=" operation @onClick="setOperation" />
  </div>
</template>

<script>
import Display from "../components/Display"
import Button from "../components/Button"

export default {
    data: function() {
        return {
            displayValue: "0",
            // O clearDisplay limpa o display em algumas situações
            clearDisplay: false,
            //Operation irá realizar a operação matemática entre dois números, o values é o array
            //onde será armazenado os valores da operação e current simboliza quais dos valores está sendo
            //trabalhado no momento (o primeiro ou o segundo)
            operation: null,
            values: [0, 0],
            current: 0
        }
    },
    components: { Button, Display },
    methods: {
        clearMemory() {
            //Esta função faz com que o sistema volte ao seu estado inicial
            Object.assign(this.$data, this.$options.data())
            
        },
        setOperation(operation) {
            //É ativado quando for a primeira vez quando um digito for adicionado
            if (this.current === 0) {
                this.operation = operation
                this.current = 1
                this.clearDisplay = true
            } else {
                const equals = operation === "="
                const currentOperation = this.operation

                try {
                    //Faz a operação matemática em cima dos dois elementos do array
                    this.values[0] = eval(
                        `${this.values[0]} ${currentOperation} ${this.values[1]}`
                    )
                } catch (e) {
                    //Exibe uma msg de erro generica, quando ocorrer  um
                    this.$emit('onError', e)
                }

                //Limpa o valor do array 1 apos realizar a operaçao
                this.values[1] = 0

                //O resultado da operação será colocado no display
                this.displayValue = this.values[0]
                //Retorna nulo se não houver mais operações a serem feitas, ou realiza a mesma
                this.operation = equals ? null : operation
                //Se o usuario clicar em =, ele irá realizar uma operaçao matematica com o segundo digito
                //caso contrario, irá limpar o display 
                this.current =  equals ? 0 : 1
                this.clearDisplay = !equals

            }
        },
        addDigit(n) {
            //Se já tiver um . na tela, não deixará que outro . será adicionado ao visor
            if (n === "." && this.displayValue.includes(".")) {
                return
            }

            //const que limpa o display
            const clearDisplay = this.displayValue === "0" || this.clearDisplay
            const currentValue = clearDisplay ? "" : this.displayValue
            
            //Exibirá mais de um dígito no displau, exibindo o numero atual e o numero que foi digitado
            const displayValue = currentValue + n
            //Tornando a função displayValue pública, para ser utilizada fora do addDigit
            this.displayValue = displayValue
            this.clearDisplay = false
            this.values[this.current] = displayValue

            // //Acrescentando o novo valor inserido no array VALUES e em CURRENT, armazenando-o
            // if (n !== ".") {
            //     const i = this.current
            //     const newValue = parseFloat(displayValue)
            //     this.values[i] = newValue
            // }
        }   
    }
}
</script>

<style>
    .calculator {
        height: 320px;
        width: 235px;
        border-radius: 5px;
        overflow: hidden;

        display: grid;
        grid-template-columns: repeat(4, 25%);
        grid-template-rows: 1fr 48px 48px 48px 48px 48px;
    }
</style>