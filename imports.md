<h1><a name="imports">World imports</a></h1>
<ul>
<li>Imports:
<ul>
<li>interface <a href="#wasi_io_poll_0_2_0"><code>wasi:io/poll@0.2.0</code></a></li>
<li>interface <a href="#wasi_clocks_monotonic_clock_0_2_0"><code>wasi:clocks/monotonic-clock@0.2.0</code></a></li>
<li>interface <a href="#wasi_clocks_wall_clock_0_2_0"><code>wasi:clocks/wall-clock@0.2.0</code></a></li>
<li>interface <a href="#wasi_clocks_timezone_0_2_0"><code>wasi:clocks/timezone@0.2.0</code></a></li>
</ul>
</li>
</ul>
<h2><a name="wasi_io_poll_0_2_0"></a>Import interface wasi:io/poll@0.2.0</h2>
<p>A poll API intended to let users wait for I/O events on multiple handles
at once.</p>
<hr />
<h3>Types</h3>
<h4><a name="pollable"></a><code>resource pollable</code></h4>
<h2><a href="#pollable"><code>pollable</code></a> represents a single I/O event which may be ready, or not.</h2>
<h3>Functions</h3>
<h4><a name="method_pollable_ready"></a><code>[method]pollable.ready: func</code></h4>
<p>Return the readiness of a pollable. This function never blocks.</p>
<p>Returns <code>true</code> when the pollable is ready, and <code>false</code> otherwise.</p>
<h5>Params</h5>
<ul>
<li><a name="method_pollable_ready.self"></a><code>self</code>: borrow&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_pollable_ready.0"></a> <code>bool</code></li>
</ul>
<h4><a name="method_pollable_block"></a><code>[method]pollable.block: func</code></h4>
<p><code>block</code> returns immediately if the pollable is ready, and otherwise
blocks until ready.</p>
<p>This function is equivalent to calling <code>poll.poll</code> on a list
containing only this pollable.</p>
<h5>Params</h5>
<ul>
<li><a name="method_pollable_block.self"></a><code>self</code>: borrow&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="poll"></a><code>poll: func</code></h4>
<p>Poll for completion on a set of pollables.</p>
<p>This function takes a list of pollables, which identify I/O sources of
interest, and waits until one or more of the events is ready for I/O.</p>
<p>The result <code>list&lt;u32&gt;</code> contains one or more indices of handles in the
argument list that is ready for I/O.</p>
<p>If the list contains more elements than can be indexed with a <code>u32</code>
value, this function traps.</p>
<p>A timeout can be implemented by adding a pollable from the
wasi-clocks API to the list.</p>
<p>This function does not return a <code>result</code>; polling in itself does not
do any I/O so it doesn't fail. If any of the I/O sources identified by
the pollables has an error, it is indicated by marking the source as
being reaedy for I/O.</p>
<h5>Params</h5>
<ul>
<li><a name="poll.in"></a><code>in</code>: list&lt;borrow&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="poll.0"></a> list&lt;<code>u32</code>&gt;</li>
</ul>
<h2><a name="wasi_clocks_monotonic_clock_0_2_0"></a>Import interface wasi:clocks/monotonic-clock@0.2.0</h2>
<p>WASI单调时钟(Monotonic Clock)是旨在让用户测量运行时间的时钟API。</p>
<p>该API旨在至少在Unix系列平台和Windows之间具有可移植性。</p>
<p>单调时钟是具有未指定初始值的时钟，并且连续读取该时钟将产生非递减值(non-decreasing values)。</p>
<hr />
<h3>Types</h3>
<h4><a name="pollable"></a><code>type pollable</code></h4>
<p><a href="#pollable"><a href="#pollable"><code>pollable</code></a></a></p>
<p>
#### <a name="instant"></a>`type instant`
`u64`
<p>时间中的时点(instant)，以纳秒(nanoseconds)为单位。
instant与一个未指定的初始值有关，
且仅能与其他相同单调时钟的instant进行比较。
<h4><a name="duration"></a><code>type duration</code></h4>
<p><code>u64</code></p>
<p>时间中的时长(duration)，以纳秒(nanoseconds)为单位。
<hr />
<h3>Functions</h3>
<h4><a name="now"></a><code>now: func</code></h4>
<p>读取时钟当前值。</p>
<p>时钟是单调的，因此重复调用此函数将产生一系列非递减值。</p>
<h5>Return values</h5>
<ul>
<li><a name="now.0"></a> <a href="#instant"><a href="#instant"><code>instant</code></a></a></li>
</ul>
<h4><a name="resolution"></a><code>resolution: func</code></h4>
<p>查询时钟的resolution。
返回与时钟周期(clock tick)对应的时间时长(duration)。</p>
<h5>Return values</h5>
<ul>
<li><a name="resolution.0"></a> <a href="#duration"><a href="#duration"><code>duration</code></a></a></li>
</ul>
<h4><a name="subscribe_instant"></a><code>subscribe-instant: func</code></h4>
<p>创建一个到达指定时刻(instant)被解析的<a href="#pollable"><code>pollable</code></a>。</p>
<h5>Params</h5>
<ul>
<li><a name="subscribe_instant.when"></a><code>when</code>: <a href="#instant"><a href="#instant"><code>instant</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="subscribe_instant.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h4><a name="subscribe_duration"></a><code>subscribe-duration: func</code></h4>
<p>创建一个在调用此函数经过指定时长(duration)后被解析的<a href="#pollable"><code>pollable</code></a>。</p>
<h5>Params</h5>
<ul>
<li><a name="subscribe_duration.when"></a><code>when</code>: <a href="#duration"><a href="#duration"><code>duration</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="subscribe_duration.0"></a> own&lt;<a href="#pollable"><a href="#pollable"><code>pollable</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi_clocks_wall_clock_0_2_0"></a>Import interface wasi:clocks/wall-clock@0.2.0</h2>
<p>WASI挂钟(Wall Clock)是旨在让用户查询当前时间的时钟API。
名称“wall”可类比于“clock on the wall(墙上的时钟)”，
它不一定是单调的，因为可能会被重置。</p>
<p>该API旨在至少在Unix系列平台和Windows之间具有可移植性。</p>
<p>挂钟(wall clock)是根据某个外部参考来测量日期和时间的时钟。</p>
<p>外部参考可能会重置，所以此时钟不一定是单调的，因此不适合测量运行时间(elapsed time)。</p>
<p>它旨在向人类报告当前的日期和时间。</p>
<hr />
<h3>Types</h3>
<h4><a name="datetime"></a><code>record datetime</code></h4>
<p>时间和日期，以秒(seconds)及纳秒(nanoseconds)为单位。</p>
<h5>Record Fields</h5>
<ul>
<li><a name="datetime.seconds"></a><code>seconds</code>: <code>u64</code></li>
<li><a name="datetime.nanoseconds"></a><code>nanoseconds</code>: <code>u32</code></li>
</ul>
<hr />
<h3>Functions</h3>
<h4><a name="now"></a><code>now: func</code></h4>
<p>读取时钟的当前值。</p>
<p>此时钟不是单调的，因此重复调用不一定会产生一系列非递减值。</p>
<p>返回的时间戳(timestamp)表示自1970-01-01T00:00:00Z以来的秒数，
也称为<a href="https://pubs.opengroup.org/onlinepubs/9699919799/xrat/V4_xbd_chap04.html#tag_21_04_16">POSIX's Seconds Since the Epoch</a>，或称<a href="https://en.wikipedia.org/wiki/Unix_time">Unix Time</a>。</p>
<p>输出的nanoseconds字段总是小于1000000000。</p>
<h5>Return values</h5>
<ul>
<li><a name="now.0"></a> <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h4><a name="resolution"></a><code>resolution: func</code></h4>
<p>查询时钟的resolution。</p>
<p>输出的nanoseconds字段总是小于1000000000。</p>
<h5>Return values</h5>
<ul>
<li><a name="resolution.0"></a> <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h2><a name="wasi_clocks_timezone_0_2_0"></a>Import interface wasi:clocks/timezone@0.2.0</h2>
<hr />
<h3>Types</h3>
<h4><a name="datetime"></a><code>type datetime</code></h4>
<p><a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></p>
<p>
#### <a name="timezone_display"></a>`record timezone-display`
<p>Information useful for displaying the timezone of a specific <a href="#datetime"><code>datetime</code></a>.</p>
<p>This information may vary within a single <code>timezone</code> to reflect daylight
saving time adjustments.</p>
<h5>Record Fields</h5>
<ul>
<li>
<p><a name="timezone_display.utc_offset"></a><a href="#utc_offset"><code>utc-offset</code></a>: <code>s32</code></p>
<p>The number of seconds difference between UTC time and the local
time of the timezone.
<p>The returned value will always be less than 86400 which is the
number of seconds in a day (24<em>60</em>60).</p>
<p>In implementations that do not expose an actual time zone, this
should return 0.</p>
</li>
<li>
<p><a name="timezone_display.name"></a><code>name</code>: <code>string</code></p>
<p>The abbreviated name of the timezone to display to a user. The name
`UTC` indicates Coordinated Universal Time. Otherwise, this should
reference local standards for the name of the time zone.
<p>In implementations that do not expose an actual time zone, this
should be the string <code>UTC</code>.</p>
<p>In time zones that do not have an applicable name, a formatted
representation of the UTC offset may be returned, such as <code>-04:00</code>.</p>
</li>
<li>
<p><a name="timezone_display.in_daylight_saving_time"></a><code>in-daylight-saving-time</code>: <code>bool</code></p>
<p>Whether daylight saving time is active.
<p>In implementations that do not expose an actual time zone, this
should return false.</p>
</li>
</ul>
<hr />
<h3>Functions</h3>
<h4><a name="display"></a><code>display: func</code></h4>
<p>Return information needed to display the given <a href="#datetime"><code>datetime</code></a>. This includes
the UTC offset, the time zone name, and a flag indicating whether
daylight saving time is active.</p>
<p>If the timezone cannot be determined for the given <a href="#datetime"><code>datetime</code></a>, return a
<a href="#timezone_display"><code>timezone-display</code></a> for <code>UTC</code> with a <a href="#utc_offset"><code>utc-offset</code></a> of 0 and no daylight
saving time.</p>
<h5>Params</h5>
<ul>
<li><a name="display.when"></a><code>when</code>: <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="display.0"></a> <a href="#timezone_display"><a href="#timezone_display"><code>timezone-display</code></a></a></li>
</ul>
<h4><a name="utc_offset"></a><code>utc-offset: func</code></h4>
<p>The same as <a href="#display"><code>display</code></a>, but only return the UTC offset.</p>
<h5>Params</h5>
<ul>
<li><a name="utc_offset.when"></a><code>when</code>: <a href="#datetime"><a href="#datetime"><code>datetime</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="utc_offset.0"></a> <code>s32</code></li>
</ul>
