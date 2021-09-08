--/* calculadora digital ********************************************************/
--/*                                                                            */
--/*                                                                            */
--/*                                                                            */
--/* ┌────┐ ┌────┐               CALCULADORA DIGITAL                            */
--/*                                                                            */
--/* └┐ ┌┘ └┐╔══╧═╗                                                             */
--/*                                                                            */
--/* │ │ │╚╗ ╔╝              DEVELOPED BY: Pablo Gutierrez, Jeisson Ruiz        */
--/*                                          Juan Jose Caicedo                 */
--/* │ │ │ ║ ║                                                                  */
--/*                                                                            */
--/* │ │ │ ║ ║                                                                  */
--/*                                                                            */
--/* │╔═╧══╗│ ║ ║             Bogota, D.C., September 8th, 2021.                */
--/*                                                                            */
--/* │╚╗ ╔╝┘ ║ ║                                                                */
--/*                                                                            */
--/* └┐║ ╚╗ ╔╝ ║         Copyright (C) Electronics Engineering Program          */
--/*                                                                            */
--/* └╚╗ ╚═╝ ╔╝                   School of Engineering                         */
--/*                                                                            */
--/* └╚╗ ╔╝                  Pontificia Universidad Javeriana                   */
--/*                                                                            */
--/* ╚═════╝                 Bogota - Colombia - South America                  */
--/*                                                                            */
--/*                                                                            */
--/*                                                                            */
--/******************************************************************************/

LIBRARY IEEE;
USE ieee.std_logic_1164.all;
----------------------------------------------------
ENTITY macro IS -- Se declaran las variables de entrada
                -- y salida globales del proyecto en el ENTITY 
 PORT(   swA    : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);--Switchs para definir el numero A
         swB    : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);--Switchs para definir el numero B
			sws    : IN  STD_LOGIC_VECTOR(1 DOWNTO 0);--Switchs para definirla operacion a realizar
			ledA   : OUT STD_LOGIC_VECTOR(6 DOWNTO 0);--Bits para encender los segmentos del numero A
			ledB   : OUT STD_LOGIC_VECTOR(6 DOWNTO 0);--Bits para encender los segmentos del numero B
		   disdec : OUT STD_LOGIC_VECTOR(6 DOWNTO 0);--Bits para encender los segmentos del numero resultante en las decenas
		   disuni : OUT STD_LOGIC_VECTOR(6 DOWNTO 0));--Bits para encender los segmentos del numero resultante en las unidades 	
END ENTITY macro;
-----------------------------------------------------
ARCHITECTURE proyecto OF macro IS

SIGNAL resa  :  STD_LOGIC_VECTOR(3 DOWNTO 0);--Resultado de la suma de 4 bits
SIGNAL Cin   :  STD_LOGIC;--Carry inicial de la suma de 4 bits
SIGNAL Cin1  :  STD_LOGIC;--Carry inicial de la resta de 4 bits
SIGNAL Cout  :  STD_LOGIC;--Carry final de la suma de 4 bits
SIGNAL Cout1 :  STD_LOGIC;--Carry final de la resta de 4 bits
SIGNAL aux   :  STD_LOGIC;--Primer auxiliar de 1 bit
SIGNAL auxi  :  STD_LOGIC;--Segundo auxiliar de 1 bit
SIGNAL out_decena :  STD_LOGIC_VECTOR(3 DOWNTO 0);--Parte de las decenas despues de analizar el negativo
SIGNAL resre :  STD_LOGIC_VECTOR(3 DOWNTO 0);--Resultado de la resta de 4 bits
SIGNAL resm  :  STD_LOGIC_VECTOR(7 DOWNTO 0);--Resultado de la multiplicacion de 8 bits
SIGNAL resat :  STD_LOGIC_VECTOR(7 DOWNTO 0);--Resultado de la suma codificada de 4 a 8 bits
SIGNAL resret:  STD_LOGIC_VECTOR(7 DOWNTO 0);--Resultado de la resta codificada de 4 a 8 bits
SIGNAL todis :  STD_LOGIC_VECTOR(7 DOWNTO 0);--Resultado de la operacion seleccionada en el selector
SIGNAL decena:  STD_LOGIC_VECTOR(3 DOWNTO 0);--Parte de las decenas 
SIGNAL unidad:  STD_LOGIC_VECTOR(3 DOWNTO 0);--Parte de las unidades
BEGIN            
  Comparador: ENTITY work.ingreso_numeros--Llamado al codigo de ingreso_numeros
  PORT MAP (ledA => ledA,--Asignacion de valor a las variables de ingreso_numeros
            ledB => ledB,
				swB  => swB,
			   swA  => swA);
				
  Suma: ENTITY work.Sum4bits--Llamado al codigo de ingreso_numeros
  PORT MAP (swA  => swA,--Asignacion de valor a las variables de ingreso_numeros
            swB  => swB,
			   resa => resa,
				Cin  => '0',
			   Cout => Cout);
				
  Resta: ENTITY work.adicion_negativa--Llamado al codigo de adicion_negativa
  PORT MAP (swA   => swA,--Asignacion de valor a las variables de adicion_negativa
            swB   => swB,
				aux   => aux,
			   resre => resre,
				Cin1  => '0',
			   Cout1 => Cout1);
				
	Multiplicacion: ENTITY work.MultA_B--Llamado al codigo de MultA_B
	PORT MAP (swA  => swA,--Asignacion de valor a las variables de MultA_B
	          swB  => swB,
				 resm => resm);
				 
	Conversor_resta: ENTITY work.bcd_1--Llamado al codigo de bcd_1
	PORT MAP (resre  => resre,--Asignacion de valor a las variables de bcd_1
	          Cout1  => Cout1,
	          resret => resret);
				 
	Conversor_suma: ENTITY  work.bcd_2--Llamado al codigo de bcd_2
	PORT MAP (resa  =>  resa,--Asignacion de valor a las variables de bcd_2
	          Cout  =>  Cout,
	          resat =>  resat);
				 
	Selector: ENTITY work.selector--Llamado al codigo de selector
	PORT MAP (resat  => resat,--Asignacion de valor a las variables de selector
	          auxi   => auxi,
	          resret => resret,
				 resm   => resm,
				 sws    => sws,
				 todis  => todis);
				 
	Separador: ENTITY work.bcd--Llamado al codigo de bcd
	PORT MAP (todis  => todis,--Asignacion de valor a las variables de bcd
	          decena => decena,
				 unidad => unidad);
				 
   Negativo: ENTITY work.predis--Llamado al codigo de predis
	PORT MAP (out_decena => out_decena,--Asignacion de valor a las variables de predis
	          decena => decena,
				 aux    => aux,
				 auxi   => auxi,
				 swA    => swA,
				 swB    => swB,
				 sws    => sws);
	
	BCD_7segmentos: ENTITY work.display--Llamado al codigo de display
	PORT MAP (  out_decena => out_decena,--Asignacion de valor a las variables de display
	            unidad => unidad,
					disdec => disdec,
					disuni => disuni);
				 
END ARCHITECTURE proyecto;
