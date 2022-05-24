# HLS Modules

## Question
How does Vivado HLS create modules from function calls? 
For example, if we have one function and you call it twice, does it create two modules?
If I want two modules of the same function, shall I duplicate the function to `f0` and `f1` and then call each?

## Answer
- `single_module.prj`: one conv function in kernel.
```
+---------------------+---------+-------+---------+---------+-----+
|         Name        | BRAM_18K| DSP48E|    FF   |   LUT   | URAM|
+---------------------+---------+-------+---------+---------+-----+
|DSP                  |        -|      -|        -|        -|    -|
|Expression           |        -|      -|        -|        -|    -|
|FIFO                 |        -|      -|        -|        -|    -|
|Instance             |        -|      5|      619|      739|    -|
|Memory               |        -|      -|        -|        -|    -|
|Multiplexer          |        -|      -|        -|       15|    -|
|Register             |        -|      -|        3|        -|    -|
+---------------------+---------+-------+---------+---------+-----+
|Total                |        0|      5|      622|      754|    0|
+---------------------+---------+-------+---------+---------+-----+
```
- `double_call.prj`: one conv function, called twice with different inputs.
```
+---------------------+---------+-------+---------+---------+-----+
|         Name        | BRAM_18K| DSP48E|    FF   |   LUT   | URAM|
+---------------------+---------+-------+---------+---------+-----+
|DSP                  |        -|      -|        -|        -|    -|
|Expression           |        -|      -|        0|        2|    -|
|FIFO                 |        -|      -|        -|        -|    -|
|Instance             |        -|     10|     1238|     1478|    -|
|Memory               |        -|      -|        -|        -|    -|
|Multiplexer          |        -|      -|        -|       15|    -|
|Register             |        -|      -|        4|        -|    -|
+---------------------+---------+-------+---------+---------+-----+
|Total                |        0|     10|     1242|     1495|    0|
+---------------------+---------+-------+---------+---------+-----+
|Available SLR        |     1344|   3008|   869120|   434560|  320|
+---------------------+---------+-------+---------+---------+-----+
|Utilization SLR (%)  |        0|   ~0  |    ~0   |    ~0   |    0|
+---------------------+---------+-------+---------+---------+-----+
|Available            |     4032|   9024|  2607360|  1303680|  960|
+---------------------+---------+-------+---------+---------+-----+
|Utilization (%)      |        0|   ~0  |    ~0   |    ~0   |    0|
+---------------------+---------+-------+---------+---------+-----+
```
- `double_func.prj`: two identical conv functions.
```
+---------------------+---------+-------+---------+---------+-----+
|         Name        | BRAM_18K| DSP48E|    FF   |   LUT   | URAM|
+---------------------+---------+-------+---------+---------+-----+
|DSP                  |        -|      -|        -|        -|    -|
|Expression           |        -|      -|        0|        2|    -|
|FIFO                 |        -|      -|        -|        -|    -|
|Instance             |        -|     10|     1238|     1478|    -|
|Memory               |        -|      -|        -|        -|    -|
|Multiplexer          |        -|      -|        -|       15|    -|
|Register             |        -|      -|        4|        -|    -|
+---------------------+---------+-------+---------+---------+-----+
|Total                |        0|     10|     1242|     1495|    0|
+---------------------+---------+-------+---------+---------+-----+
```

The answer is that, if you want multiple modules of the same function, you don't have to copy-and-paste the functions and call each. Just call the same function twice with different inputs, you will get two modules synthesized.