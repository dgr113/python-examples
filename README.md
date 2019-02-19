# Create new sub-loop in existing asyncio event loop


```
# coding: utf-8

import asyncio
import ipywidgets as widgets
import pandas as pd
from multiprocessing.pool import ThreadPool

""" This example need to be installed <ipywidgets> package:
    https://ipywidgets.readthedocs.io/en/stable/ """



def async_func(start_date, end_date):
    await asyncio.sleep(3)
    return 200


def get_results(loop, start_date, end_date):
    """ Main (not async) function for getting results """

    asyncio.set_event_loop(loop)
    results = loop.run_until_complete( async_func(start_date, end_date) )

    return results


def main(start_date, end_date):
    """ Creating a new Thread is necessary to start a separate (from the main IPython) event loop 
        This is necessary for the correct operation of standard (non-async) widgets)
    """

    loop = asyncio.new_event_loop()
    with ThreadPool(1) as pool:
        results = pool.apply(get_results, args=[loop, start_date, end_date])
        return results


widgets.interact_manual(
    main,
    start_date=widgets.DatePicker(value=pd.to_datetime('2018-01-01')),
    end_date=widgets.DatePicker(value=pd.to_datetime('2018-01-01'))
)
```