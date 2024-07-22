QuantDigger 0.6.0
==================
    
QuantDigger is a Python based quantitative backtesting framework. It draws on the concise strategic syntax of mainstream commercial software such as TB and Pyramid, while
Avoiding the limitations of their built-in programming languages, using the general-purpose language Python as a strategy development tool. Compared to zipline_ and pyalgotrade_,
Quant Digger's strategy syntax is closer to the habits of strategy developers. The current functions include stock backtesting and futures backtesting. Support stock selection, arbitrage, timing, and portfolio strategies. It comes with a simple strategy and k-line display interface based on matplotlib, which can meet the basic backtesting needs of quantitative enthusiasts. The design also takes into account real-time trading, and if there is time in the future, a trading interface will also be added.


**Due to personal time and work constraints, this project will no longer be maintained**



file
-----
Wiki documentation_


Dependency library
-------
* matplotlib 
* numpy
* logbook
* pandas 
* progressbar2
* zmq
*BeautifulSoup 4 (required for tushare)
*Lxml (required for Tushare)
*Tushare_ (a very powerful stock information crawling tool)
*Python datatil (optional)
* IPython
* TA-Lib

*You can install dependency libraries using pip:
>>> pip install -r requirements/requirements.txt
*If pypi source timeout occurs:
>>> pip install -r requirements/requirements.txt -i  http://pypi.douban.com/simple  --trusted-host pypi.douban.com

*TA Lib may encounter errors when installed directly through pip,
*Go to http://www.lfd.uci.edu/ ~Gohlke/pythonlibs/# ta lib Download the corresponding version and install it using the command, such as
>>> pip install TA_Lib-0.4.10-cp36-cp36m-win_amd64.whl
*Anaconda users can use
>>> conda install -c quantopian ta-lib

*Finance dependency
*Installation https://github.com/matplotlib/mpl_finance


Strategy Combination Demo
-----------

Source code
~~~~

.. code:: py


from quantdigger import (
Strategy,
MA,
DateTimeSeries,
NumberSeries,
set_config,
add_strategies,
Profile
)


class DemoStrategy(Strategy):
Strategy A1

def on_init(self, ctx):
Initialize Data
ctx.ma10 = MA(ctx.close, 10, 'ma10', 'y', 1)
ctx.ma20 = MA(ctx.close, 20, 'ma20', 'b', 1)
ctx.dt = DateTimeSeries()
ctx.month_price = NumberSeries()

def on_bar(self, ctx):
ctx.dt.update(ctx.datetime)
if ctx.dt[1].month !=  ctx.dt[0].month:
ctx.month_price.update(ctx.close)
if ctx.curbar > 20:
if ctx.pos() == 0 and ctx.ma10[2] < ctx.ma20[2] and ctx.ma10[1] > ctx.ma20[1]:
ctx.buy(ctx.close, 1)
ctx.plot_text("buy", 1,  ctx.curbar, ctx.close,  "buy", 'black', 15)
elif ctx.pos() > 0 and ctx.ma10[2] > ctx.ma20[2] and \
ctx.ma10[1] < ctx.ma20[1]:
ctx.plot_text("sell", 1,  ctx.curbar, ctx.close,  "sell", 'blue', 15)
ctx.sell(ctx.close, ctx.pos())
ctx.plot_line("month_price", 1,  ctx.curbar, ctx.month_price, 'y--', lw=2)
return

def on_exit(self, ctx):
return


class DemoStrategy2(Strategy):
Strategy A2

def on_init(self, ctx):
Initialize Data
ctx.ma50 = MA(ctx.close, 50, 'ma50', 'y', 2)
ctx.ma100 = MA(ctx.close, 100, 'ma100', 'black', 2)

def on_symbol(self, ctx):
pass

def on_bar(self, ctx):
if ctx.curbar > 100:
if ctx.pos() == 0 and ctx.ma50[2] < ctx.ma100[2] and ctx.ma50[1] > ctx.ma100[1]:
ctx.buy(ctx.close, 1)
elif ctx.pos() > 0 and ctx.ma50[2] > ctx.ma100[2] and \
ctx.ma50[1] < ctx.ma100[1]:
ctx.sell(ctx.close, ctx.pos())

return

def on_exit(self, ctx):
return


if __name__ == '__main__':
import timeit
start = timeit.default_timer()
set_config({'source': 'csv'})
profiles = add_strategies(['BB.SHFE-1.Day'], [
{
'strategy': DemoStrategy('A1'),
'capital': 50000.0 * 0.5,
},
{
'strategy': DemoStrategy2('A2'),
'capital': 50000.0 * 0.5,
}
])
stop = timeit.default_timer()
Print ("Running time:% d seconds"% (stop start))

#Draw K-line and trading signal line
from quantdigger.digger import finance, plotting
s = 0
#Draw the funding curves for strategy A1, strategy A2, and the combination
curve0 = finance.create_equity_curve(profiles[0].all_holdings())
curve1 = finance.create_equity_curve(profiles[1].all_holdings())
curve = finance.create_equity_curve(Profile.all_holdings_sum(profiles))
plotting.plot_strategy(profiles[0].data(), profiles[0].technicals(),
profiles[0].deals(),  curve0.equity.values,
profiles[0].marks())
#Draw a net asset value curve
plotting.plot_curves([curve.networth])
#Print statistical information
print(finance.summary_stats(curve, 252))


Strategy results
~~~~~~~

*K-line and signal line

The K-line display uses a built-in linkage window control in the system, which is controlled by a blue slider to adjust the display area. The display area can be changed by dragging the mouse.
`Use the up and down arrow keys to zoom in and out.

.. image:: doc/images/plot.png
:width: 500px

*Two strategies and portfolio funding curves.
  
.. image:: doc/images/figure_money.png
:width: 500px

*Historical net value of the combination
  
.. image:: doc/images/figure_networth.png
:width: 500px

*Statistical results

::
       
>>> [('Total Return', '-0.99%'), ('Sharpe Ratio', '-5.10'), ('Max Drawdown', '1.72%'), ('Drawdown Duration', '3568')]


.. _TeaEra:  https://github.com/TeaEra
.. _deepfish:  https://github.com/deepfish
.. _wondereamer:  https://github.com/wondereamer
.. _HonePhy:  https://github.com/HonePhy
.. _tushare:  https://github.com/waditu/tushare
.. _Jimmy:  https://github.com/jimmysoa
.. _vodkabuaa:  https://github.com/vodkabuaa
.. _ongbe:  https://github.com/ongbe
.. _pyalgotrade:  https://github.com/gbeced/pyalgotrade
.. _zipline:  https://github.com/quantopian/zipline
.. W_iki document: https://github.com/QuantFans/quantdigger/wiki


edition
~~~~

**Version 0.6.0 May 28, 2019**

*Refactoring the backtesting engine to make its design more reasonable and concise.

**Version 0.5.1, July 13, 2017**

*On the basis of the original version 0.5.0, it has been changed to support Python 3.6

**Version 0.5.0, January 8, 2017**

*Improve documentation
*Data source configurable
*Add an interactive framework between shell, interface, and backtesting engine

**0.3.0 version 2015-12-09**

*Redesign the backtesting engine to support combination backtesting and stock selection
*Refactoring the data module

**Version 0.2.0, August 18, 2015**

*Fix bankruptcy bug in stock backtesting
*Fix bug in backtesting equity calculation
*Separate the calculation of trading signal pairs from backtesting code
*Move backtesting financial indicators to Digger/Finance
*Add some data structures, add some data structure fields
*Add several MongoDB related functions
    
**Version 0.1.0 June 16, 2015**

*Strategy backtesting function for praising varieties
*Simple interaction
*Indicator, K-line drawing
