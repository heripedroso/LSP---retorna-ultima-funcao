# LSP---retorna-ultima-funcao
Função que retorna a última função atribuída ao colaborador em um determinado período.

## Definir estrutura de tabela em Funções Globais
```
Definir Tabela objColaborador[2] = {   
                                     Numero nNumCPF;
                                     Numero nNumEmp;
                                     Numero nTipCol;                                     
                                     Numero nNumCad; 
                                     Numero nNumCra;
                                     Alfa aFuncao;
                                     Alfa aCodCargo;
                                   };

Definir Tabela objParametrosEntrada[1] = {   
                                     Data dataPrimeiroDiaDoMes;
                                     Data dataPrimeiroDiaDoMes; 
                                   };
```

## Preencher valores antes de recuperar informações de função.
```
objColaborador[1].nNumCPF = R034FUN.NumCpf;
objColaborador[1].nNumEmp = R034FUN.NumEmp;
objColaborador[1].nTipCol = R034FUN.TipCol;  
objColaborador[1].nNumCad = R034FUN.NumCad;
objColaborador[1].nNumCra = R034FUN.NumCra;
objColaborador[1].aFuncao = "";
objColaborador[1].aCodigoCargo = "";
/*
Em algum momento deve-se rodar a função retorna2T() para que as informações do 2º vínculo sejam preenchidas.
objColaborador[2].nNumCPF = 0;
objColaborador[2].nNumEmp = 0;
objColaborador[2].nTipCol = 0;  
objColaborador[2].nNumCad = 0;
objColaborador[2].nNumCra = 0;
objColaborador[2].aFuncao = "";
objColaborador[2].aCodigoCargo = "";
*/
```

## Recupera informações da função mais recente.
* Função é uma atribuição temporária. Geralmente é um cargo de liderança atribuído a um colega apenas durante o período de férias/afastamento da liderança titular.
```
Definir Funcao retornaUltimaFuncao(Numero nVinculo);

Funcao retornaUltimaFuncao(Numero nVinculo);
{

     @ ----------------Carrega valores ------------------------ @
     nNumEmp = objColaborador[nVinculo].nNumEmp; 
     nTipCol = objColaborador[nVinculo].nTipCol;
     nNumCad = objColaborador[nVinculo].nNumCad;
     
     @ Trata as entradas Data Início e Data Fim @     
     Definir Data dDataZero;
     MontaData (31, 12, 1900, dDataZero);     
     Definir Data dInicioMes; dInicioMes = objParamentrosEntrada[1].dDataInicioDoMes;
     Definir Data dFimMes; dFimMes = objParamentrosEntrada[1].dDataFimDoMes;
     
     @ ------- Acessa tabelas para recuperar informações de função ----------- @
     Definir Cursor cC54;
       
     cC54.SQL "Select r024car.codcar, r024car.TitRed,r024car.TitCAR \
                 From R038HFU r038hfu, R024CAR r024car \
                 where r038hfu.NumEmp = :nNumemp And r038hfu.TipCol = :nTipCol And r038hfu.NumCad = :nNumCad \ 
                 and r024car.CodCar = r038hfu.CodCar \
                 And r038hfu.DatIni=(Select max(Tab2.DatIni) \
                                     From R038HFU Tab2 Where Tab2.NumEmp = :nNumemp And Tab2.TipCol = :nTipCol And Tab2.NumCad = :nNumCad \
                                     and tab2.DatIni <= :dFimMes \ 
                                     and ( \
                                         Tab2.datfim <> :dDataZero and Tab2.datfim >= :dInicioMes  \
                                         or \
                                         Tab2.datfim = :dDataZero  \
                                     ))";
     cC54.AbrirCursor();
     
     @ ----------- Grava valores na estrutura objColaborado[nVinculo] -------- @
     Enquanto (cC54.Achou) {
           objColaborador[nVinculo].aFuncao = cC54.TitCar;
           objColaborador[nVinculo].aCodCargo = cC54.codcar;

           cC54.proximo();
     }
     
     cC54.FecharCursor();   
}
```
     
