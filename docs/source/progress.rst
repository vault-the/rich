.. _progress:

Progress Display
================

Rich can display continuously updated information regarding the progress of long running tasks / file copies etc. The information displayed is configurable, the default will display a description of the 'task', a progress bar, percentage complete, and estimated time remaining.

Rich progress display supports multiple tasks, each with a bar and progress information. You can use this to track concurrent tasks where the work is happening in threads or processes.

To see how the progress display looks, try this from the command line::

    python -m rich.progress

Basic Usage
-----------

For basic usage call the :func:`~rich.progress.track` function, which accepts a sequence (such as a list or range object) and an optional description of the job you are working on. The track method will yield values from the sequence and update the progress information on each iteration. Here's an example::

    from rich.progress import track

    for n in track(range(n), description="Processing..."):
        do_work(n)

Advanced usage
--------------

If you require multiple tasks in the display, or wish to configure the columns in the progress display, you can work directly with the :class:`~rich.progress.Progress` class. To use, add a task(s) with (:meth:`~rich.progress.Progress.add_task`) and update progress with :meth:`~rich.progress.Progress.update`.

The Progress class is designed to be used as a *context manager* which will start and stop the progress display automatically.

Here's a simple example::

    from rich.progress import Progress

    with Progress() as progress:

        task1 = progress.add_task("[red]Downloading...", total=1000)
        task2 = progress.add_task("[green]Processing...", total=1000)
        task3 = progress.add_task("[cyan]Cooking...", total=1000)

        while not progress.finished:
            progress.update(task1, advance=0.5)
            progress.update(task2, advance=0.3)
            progress.update(task3, advance=0.9)
            time.sleep(0.02)

Updating Tasks
~~~~~~~~~~~~~~

When you add a task you get back a `Task ID`. Use this ID to call :meth:`~rich.progress.Progress.update` whenever you have completed some work, or any information has changed.

Auto refresh
~~~~~~~~~~~~

By default, the progress information will auto refresh at 10 times a second. Refreshing in a predictable rate can make numbers more readable if they are updating very quickly. Auto refresh can also prevent excessive rendering to the terminal.

You can disable auto-refresh by setting ``auto_refresh=False`` on the constructor and call :meth:`~rich.progress.Progress.refresh` manually when there are updates to display.

Columns
~~~~~~~

You may customize the columns in the progress display with the positional arguments to the :class:`~rich.progress.Progress` constructor. The columns are specified as either a format string or a :class:`~rich.progress.ProgressColumn` object.

Format strings will be rendered with a single value `"task"` which will be a :class:`~rich.progress.Task` instance. For example ``"{task.description}"`` would display the task description in the column.

The defaults are roughly equivalent to the following::

    progress = Progress(
        "[progress.description]{task.description}",
        BarColumn(),
        "[progress.percentage]{task.percentage:>3.0f}%",
        TimeRemainingColumn(),
    )

The following column objects are available:

- :class:`~rich.progress.BarColumn` Displays the bar.
- :class:`~rich.progress.TimeRemainingColumn` Displays the estimated time remaining.
- :class:`~rich.progress.FileSizeColumn` Displays progress as file size (assumes the steps are bytes).
- :class:`~rich.progress.TransferSpeedColumn` Displays transfer speed (assumes the steps are bytes.


Example
-------

See `downloader.py <https://github.com/willmcgugan/rich/blob/master/examples/downloader.py>`_ for a realistic application of a progress display. This script can download multiple concurrent files while displaying progress with transfer speed and file size.
