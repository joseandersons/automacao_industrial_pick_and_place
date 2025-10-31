# Dupla:  José Anderson da Silva // Higor de Lima Gomes

# Pick & Place (Basic) — Factory I/O + TIA Portal

## Descrição do problema
Implementar, em Ladder (TIA Portal), o controle de uma célula **Pick & Place**: retirar itens da esteira de entrada e depositá-los na esteira de saída usando um manipulador cartesiano (eixos X/Y/Z e garra). O ciclo inicia em **Start**, para em **Stop**, com **Reset** e **Emergência**, e modos **Auto/Manual**.

<p align="center">
  <img width="2556" height="1367" alt="image" src="https://github.com/user-attachments/assets/dddc7c08-6857-4bd0-bfda-5d218259b64f" />
</p>
<p align="center"><b>Figura 1:</b> Cena Pick & Place (Basic).</p>

## Estratégia de implementação
- **Controle & Segurança:** intertravamentos de Start/Stop/Reset/E-Stop, seleção Auto/Manual e sinalização por luzes.
- **Máquina de Estados:** estados codificados em centenas (000…700) com transições por sensores (`Item at entry/exit`, `Item detected`) e flags de movimento (`Moving X/Y`).

### Tabela-verdade — Atuadores por estado
| ESTADO | Entry conveyor | Exit conveyor | Move Z | Move X | Grab |
|:-----:|:--------------:|:-------------:|:------:|:------:|:----:|
| 000   | 0              | 0             | 0      | 0      | 0    |
| 100   | 1              | 0             | 0      | 0      | 0    |
| 200   | 0              | 0             | 1      | 0      | 1    |
| 300   | 0              | 0             | 0      | 0      | 1    |
| 400   | 0              | 0             | 0      | 1      | 1    |
| 500   | 0              | 0             | 1      | 1      | 1    |
| 600   | 0              | 0             | 0      | 1      | 0    |
| 700   | 0              | 1             | 0      | 0      | 0    |

## Tabela de endereçamento
| Endereço | TAG               | TIPO |
|---------:|-------------------|------|
| %IW64    | Int               | Int  |
| %IW66    | Int               | Int  |
| %QW30    | Counter (lo)      | Int  |
| %QW32    | Counter (hi)      | Int  |
| %I0.0    | Item at entry     | Bool |
| %I0.1    | Item at exit      | Bool |
| %I0.2    | Moving X          | Bool |
| %I0.3    | Moving Y          | Bool |
| %I0.4    | Item detected     | Bool |
| %I0.5    | Start             | Bool |
| %I0.6    | Reset             | Bool |
| %I0.7    | Stop              | Bool |
| %I1.0    | Emergency stop    | Bool |
| %I1.1    | Auto              | Bool |
| %I1.2    | FACTORY I/O (R…)  | Bool |
| %I1.3    | —                 | Bool |
| %I1.4    | —                 | Bool |
| %I1.5    | —                 | Bool |
| %Q0.0    | Entry conveyor    | Bool |
| %Q0.1    | Exit conveyor     | Bool |
| %Q0.2    | Move X            | Bool |
| %Q0.3    | Move Y            | Bool |
| %Q0.4    | Start light       | Bool |
| %Q0.5    | Reset light       | Bool |
| %Q0.6    | Stop light        | Bool |
| %Q1.0    | —                 | Bool |
| %Q1.1    | —                 | Bool |

## Como executar
1. **TIA Portal**
   - Abra o projeto e **Compile**: Hardware (rebuild) e Software (All blocks).
2. **Factory I/O**
   - Abra a cena **Pick & Place (Basic)**.
   - Mapeie I/Os conforme a **Tabela de endereçamento**.
   - Driver:
     - CLP físico S7-1200/1500: *Siemens S7-1200/1500 (ISO on TCP)*.
     - Simulação: preferir **PLCSIM Advanced** para comunicação direta.
3. **Operação**
   - Inicie o cenário, pressione **Start** no painel.
   - Use **Stop/Reset/E-Stop** quando necessário.
   - Verifique a sequência pelos estados (000→700) e pelas luzes.

