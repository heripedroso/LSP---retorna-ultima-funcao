# LSP---retorna-ultima-funcao
Função que retorna a última função atribuída ao colaborador em um determinado período.

## Definir estrutura de tabela em Funções Globais
```
Definir Tabela objColaborador[1] = {   
                                     Numero nNumEmp;
                                     Numero nTipCol;                                     
                                     Numero nNumCad; 
                                     Numero nNumCra;
                                     Alfa aFuncao;
                                     Alfa aCodigoCargo;
                                     Numero n2TNumCad;  @ Cadastro do 2º vínculo (2º teto). Pode ocorrer caso o colaborador seja da área assistencial (médico, enfermeiro, psicólogo, etc). @
                                     Numero n2TNumCra;  @ Crachá do 2º vínculo (2º teto). Pode ocorrercaso o colaborador seja da área assistencial (médico, enfermeiro, psicólogo, etc). @
                                     Alfa a2TFuncao;
                                     Alfa a2TCodigoCargo;
                                   };

Definir Tabela objParamentrosEntrada[1] = {   
                                     Data dataPrimeiroDiaDoMes;
                                     Data dataPrimeiroDiaDoMes; 
                                   };
```

## Inserir trecho em seção detalhe
```
objColaborador[1].nNumEmp = R034FUN.NumEmp;
objColaborador[1].nTipCol = R034FUN.TipCol;  
objColaborador[1].nNumCad = R034FUN.NumCad;
objColaborador[1].nNumCra = R034FUN.NumCra;
objColaborador[1].aFuncao = "";
objColaborador[1].aCodigoCargo = "";
objColaborador[1].n2TNumCad = 0;
objColaborador[1].n2TNumCra = 0;
objColaborador[1].a2TFuncao = "";
objColaborador[1].a2TCodigoCargo = "";
```

## Definir estrutura de tabela em Funções Globais
```
Definir Funcao retornaUltimaFuncao(Numero nVerifica2T);

Funcao retornaUltimaFuncao(Numero nVerifica2T);
{

     @ Trata as entradas Data Início e Data Fim @     
     Definir Data dDataZero;
     MontaData (31, 12, 1900, dDataZero);
     @ dataPrimeiroDiaDoMes - variável definida em seção anterior.  @
     @ dataUltimoDiaDoMes - variável definida em seção anterior. @
     @ ---------------------------------------- @

     nNumEmp = objColaborador[1].nNumEmp; 
     nTipCol = objColaborador[1].nTipCol;
     nNumCad = objColaborador[1].nNumCad;
     Se(nVerifica2T = 1){
           nNumCad = objColaborador[1].n2TNumCad;
     }
     
     Definir Cursor cC54;
       
     cC54.SQL "Select r024car.codcar, r024car.TitRed,r024car.TitCAR \
                 From R038HFU r038hfu, R024CAR r024car \
                 where r038hfu.NumEmp = :nNumemp And r038hfu.TipCol = :nTipCol And r038hfu.NumCad = :nNumCad \ 
                 and r024car.CodCar = r038hfu.CodCar \
                 And r038hfu.DatIni=(Select max(Tab2.DatIni) \
                                     From R038HFU Tab2 Where Tab2.NumEmp = :nNumemp And Tab2.TipCol = :nTipCol And Tab2.NumCad = :nNumCad \
                                     and tab2.DatIni <= :dataUltimoDiaDoMes \ 
                                     and ( \
                                         Tab2.datfim <> :dDataZero and Tab2.datfim >= :dataPrimeiroDiaDoMes  \
                                         or \
                                         Tab2.datfim = :dDataZero  \
                                     ))";
     cC54.AbrirCursor();
     
     Enquanto (cC54.Achou) {
           Se(nVerifica2T = 0){
                objColaborador[1].aFuncao = cC54.TitCar;
                objColaborador[1].aCodigoCargo = cC54.codcar;
           } 
           Se(nVerifica2T = 1){
                objColaborador[1].a2TFuncao = cC54.TitCar;
                objColaborador[1].a2TCodigoCargo = cC54.codcar;
           } 
           cC54.proximo();
     }
     cC54.FecharCursor();
     
}


     
```
     
