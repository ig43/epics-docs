NTScalar
============================================

An IOC allows to talk to devices e.g. via ethernet. Create a directory for
the IOCs. For example ``$HOME/EPICS/IOCs``

::

    cd $HOME/EPICS
    mkdir IOCs
    cd IOCs

Create a top for an IOC called ``sampleIOC``

<h2>Normative Types NTScalar</h2>

<p>NTScalar is the EPICS V4 Normative Type that describes a single
scalar value plus metadata:</p>


<p>Its structure is defined to be:</p>

<pre>
epics:nt/NTScalar:1.0
    <span class="nterm">scalar_t</span>   value
    string descriptor                   : optional
    alarm_t alarm                       : optional
        int severity
        int status
        string message
    time_t timeStamp                    : optional
        long secondsPastEpoch
        int nanoseconds
        int userTag
    display_t display                   : optional
        double limitLow
        double limitHigh
        string description
        string format
        string units
    control_t control                   : optional
        double limitLow
        double limitHigh
        double minStep
    {&lt;field-type&gt; &lt;field-name&gt;}0+  // additional fields
</pre>

<p>where <span class="nterm">scalar_t</span> indicates a choice of scalar:</p>

    <pre>
<span class="nterm">scalar_t</span> :=

   boolean | byte |  ubyte |  short |  ushort |
   int |  uint |  long |  ulong |  float |  double |  string
</pre>


<h3>NTScalarBuilder</h3>

<p>This is a class that creates the introspection and data instances for NTScalar and
an a NTScalar instance itself.</p>

<p><b>ntscalar.h</b> defines the following:</p>

<pre>
class NTScalar;
typedef std::tr1::shared_ptr&lt;NTScalar&gt; NTScalarPtr;

class NTScalarBuilder
{
public:
    POINTER_DEFINITIONS(NTScalarBuilder);
    shared_pointer value(ScalarType scalarType);
    shared_pointer addDescriptor();
    shared_pointer addAlarm();
    shared_pointer addTimeStamp();
    shared_pointer addDisplay();
    shared_pointer addControl();
    StructureConstPtr createStructure();
    PVStructurePtr createPVStructure();
    NTScalarPtr create();
    shared_pointer add(
         string const &amp; name,
         FieldConstPtr const &amp; field);
private:
    // ... remainder of class definition
}
</pre>
where
<dl>
  <dt>value</dt>
    <dd>Sets the scalar type for the <code>value</code> field.
     This must be specified or a call of <code>create()</code>,
     <code>createStructure()</code> or <code>createPVStructure()</code>
     will throw an exception (<code>std::runtime_error</code>).</dd>
</dl>

<p>and all other functions are described in the sections <a href="#features_common_to_all_normative_type_builder_classes">Features common to all Normative Type Builder classes</a> and <a href="#normative_type_property_features">Normative Type Property Features</a>.</p>

<p>An <code>NTScalarArrayBuilder</code> can be used to create multiple <code>Structure</code>, <code>PVStructure</code> and/or <code>NTScalar</code> instances.</p>

<p>A call of <code>create()</code>, <code>createStructure()</code> or <code>createPVStructure()</code> clears all internal data. This includes the effect of calling <code>value()</code> as well all calls of optional field/property data functions and additional field functions.</p>



<h4>NTScalarBuilder Examples</h4>
<p>An example of creating an NTScalar instance is:</p>
<pre>
NTScalarBuilderPtr builder = NTScalar::createBuilder();
NTScalarPtr ntScalar = builder-&gt;
    value(pvInt)-&gt;
    addDescriptor()-&gt;
    addAlarm()-&gt;
    addTimeStamp()-&gt;
    addDisplay()-&gt;
    addControl()-&gt;
    create();
</pre>

<h3>NTScalar</h3>
<p><b>ntscalar.h</b> defines the following:</p>
<pre>
class NTScalar;
typedef std::tr1::shared_ptr&lt;NTScalar&gt; NTScalarPtr;

class NTScalar
{
public:
    POINTER_DEFINITIONS(NTScalar);
    ~NTScalar() {}
    static const string URI;
    static shared_pointer wrap(PVStructurePtr const &amp; pvStructure);
    static shared_pointer wrapUnsafe(PVStructurePtr const &amp; pvStructure);
    static bool is_a(StructureConstPtr const &amp; structure);
    static bool is_a(PVStructurePtr const &amp; pvStructure);
    static bool isCompatible(StructureConstPtr const &amp; structure);
    static bool isCompatible(PVStructurePtr const &amp; pvStructure);
    static NTScalarBuilderPtr createBuilder();

    bool attachTimeStamp(PVTimeStamp &amp;pvTimeStamp) const;
    bool attachAlarm(PVAlarm &amp;pvAlarm) const;
    bool attachDisplay(PVDisplay &amp;pvDisplay) const;
    bool attachControl(PVControl &amp;pvControl) const;

    PVStructurePtr getPVStructure() const;
    PVStructurePtr getTimeStamp() const;
    PVStructurePtr getAlarm() const;
    PVStructurePtr getDisplay() const;
    PVStructurePtr getControl() const;

    PVFieldPtr getValue() const;

    template&lt;typename PVT&gt;
    std::tr1::shared_ptr&lt;PVT&gt; getValue() const
private:
    // ... remainder of class definition
}
</pre>
<p>where</p>
<dl>
   <dt>getValue()</dt>
       <dd>Returns the <code>value</code> field. The template version returns the type supplied in the template argument.</dd>
</dl>
<p>and all other functions are described in the sections <a href="#features_common_to_all_normative_type_wrapper_classes">Features common to all Normative Type Wrapper classes</a> and <a href="#normative_type_property_features">Normative Type Property Features</a>.</p>


<h2>Normative Type NTScalarArray</h2>

    <p>NTScalarArray is the EPICS V4 Normative Type that describes an
    array of values, plus metadata. All the elements of the array of the
    same scalar type.</p>

<pre>
epics:nt/NTScalarArray:1.0
    <span class="nterm">scalar_t[]</span>   value
    string descriptor              : optional
    alarm_t alarm                  : optional
        int severity
        int status
        string message
    time_t timeStamp               : optional
        long secondsPastEpoch
        int nanoseconds
        int userTag
    display_t display              : optional
        double limitLow
        double limitHigh
        string description
        string format
        string units
    {&lt;field-type&gt; &lt;field-name&gt;}0+  // additional fields
</pre>

<p>where <span class="nterm">scalar_t[]</span> indicates a choice of scalar array:</p>

    <pre>
<span class="nterm">scalar_t[]</span> :=

   boolean[] | byte[] |  ubyte[] |  short[] |  ushort[] |
   int[] |  uint[] |  long[] |  ulong[] |  float[] |  double[] |  string[]
</pre>



<h3>NTScalarArrayBuilder</h3>
<p><b>ntscalarArray.h</b> defines the following:</p>
<pre>
class NTScalarArray;
typedef std::tr1::shared_ptr&lt;NTScalarArray&gt; NTScalarArrayPtr;

class NTScalarArrayBuilder
{
public:
    POINTER_DEFINITIONS(NTScalarArrayBuilder);
    shared_pointer value(ScalarType elementType);
    shared_pointer addDescriptor();
    shared_pointer addAlarm();
    shared_pointer addTimeStamp();
    shared_pointer addDisplay();
    shared_pointer addControl();
    StructureConstPtr createStructure();
    PVStructurePtr createPVStructure();
    NTScalarArrayPtr create();
    shared_pointer add(
         string const &amp; name,
         FieldConstPtr const &amp; field);
private:
    // ... remainder of class definition
};
</pre>

<p>where</p>
<dl>
  <dt>value</dt>
    <dd>Sets the element type for the <code>value</code> field.
     This must be specified or a call of <code>create()</code>,
     <code>createStructure()</code> or <code>createPVStructure()</code>
     will throw an exception (<code>std::runtime_error</code>).</dd>
</dl>

<p>and all other functions are described in the sections <a href="#features_common_to_all_normative_type_builder_classes">Features common to all Normative Type Builder classes</a> and <a href="#normative_type_property_features">Normative Type Property Features</a>.</p>

<p>An <code>NTScalarArrayBuilder</code> can be used to create multiple <code>Structure</code>, <code>PVStructure</code> and/or <code>NTScalarArray</code> instances.</p>

<p>A call of <code>create()</code>, <code>createStructure()</code> or <code>createPVStructure()</code> clears all internal data. This includes the effect of calling <code>value()</code> as well all calls of optional field/property data functions and additional field functions.</p>


<h3>NTScalarArray</h3>
<p><b>ntscalarArray.h</b> defines the following:</p>
<pre>
class NTScalarArray;
typedef std::tr1::shared_ptr&lt;NTScalarArray&gt; NTScalarArrayPtr;

class NTScalarArray
{
public:
    POINTER_DEFINITIONS(NTScalarArray);
    ~NTScalarArray() {}
    static const string URI;
    static shared_pointer wrap(PVStructurePtr const &amp; pvStructure);
    static shared_pointer wrapUnsafe(PVStructurePtr const &amp; pvStructure);
    static bool is_a(StructureConstPtr const &amp; structure);
    static bool is_a(PVStructurePtr const &amp; pvStructure);
    static bool isCompatible(StructureConstPtr const &amp; structure);
    static bool isCompatible(PVStructurePtr const &amp; pvStructure);
    static NTScalarArrayBuilderPtr createBuilder();

    bool attachTimeStamp(PVTimeStamp &amp;pvTimeStamp) const;
    bool attachAlarm(PVAlarm &amp;pvAlarm) const;
    bool attachDisplay(PVDisplay &amp;pvDisplay) const;
    bool attachControl(PVControl &amp;pvControl) const;

    PVStructurePtr getPVStructure() const;
    PVStructurePtr getTimeStamp() const;
    PVStructurePtr getAlarm() const;
    PVStructurePtr getDisplay() const;
    PVStructurePtr getControl() const;

    PVFieldPtr getValue() const;
    template&lt;typename PVT&gt;
    std::tr1::shared_ptr&lt;PV&gt; getValue() const
private:
    // ... remainder of class definition
};
</pre>
where
<dl>
   <dt>getValue</dt>
       <dd>Returns the <code>value</code> field. The template version returns the type supplied in the template argument.</dd>
</dl>

<p>and all other functions are described in the sections <a href="#features_common_to_all_normative_type_wrapper_classes">Features common to all Normative Type Wrapper classes</a> and <a href="#normative_type_property_features">Normative Type Property Features</a>.</p>

