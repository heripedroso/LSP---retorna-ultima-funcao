# LSP---retorna-ultima-funcao
Função que retorna a última função atribuída ao colaborador em um determinado período.

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
                                   };

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

Definir Funcao retornaUltimaFuncao();

Funcao retornaUltimaFuncao();
{

     @ Trata as entradas Data Início e Data Fim @
     Definir Data dDataInicio;  dDataInicio = EDatInR;
     Definir Alfa aDataInicio; 
     ConverteDataBanco(dDataInicio, aDataInicio);
     
     Definir Data dDataFim;  dDataFim = EDatFiR;
     Definir Alfa aDataFim; 
     ConverteDataBanco(dDataFim, aDataFim);
     
     Definir Data dDataZero;
     Definir Alfa aDataZero; 
     Definir Alfa gCodigoCargo; gCodigoCargo = ""; 
     MontaData (31, 12, 1900, dDataZero);
     ConverteDataBanco(dDataZero, aDataZero);
     @ ---------------------------------------- @

     nNumEmp = objColaborador[1].nNumEmp; 
     nTipCol = objColaborador[1].nTipCol;
     nNumCad = objColaborador[1].nNumCad;
     
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
            objColaborador[1].aFuncao = cC54.TitCar;
            objColaborador[1].aCodigoCargo = cC54.codcar;
            cC54.proximo();
      }
     cC54.FecharCursor();
     
}
     
```
     
