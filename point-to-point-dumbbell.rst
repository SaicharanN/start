.. include:: replace.txt
.. highlight:: cpp

PointToPoint Dumbbell helper
----------------------------

About PointToPointDumbbellHelper
**********************************

PointToPointDumbbellHelper- helper to make it easier to create a dumbbell 
topology with pointToPoint links. This takes the following as parameters 
and creates the desired dumbbell model:

* number of left side leaf nodes in the dumbbell
* PointToPointHelper used to install the links between the left leaf nodes and the left-most router
* number of right side leaf nodes in the dumbbell
* PointToPointHelper used to install the links between the right leaf nodes and the right-most router
* PointToPointHelper used to install the link between the inner-routers, usually known as the bottleneck link 

Each of the Links in dumbbell model is a pointToPoint link and thereby,
are connected via an PointToPointChannel. This channel models two wires 
transmitting bits at the data rate specified by the
source net device. There is no overhead beyond the eight bits per byte of the
packet sent. That is, we do not model Flag Sequences, Frame Check Sequences nor
do we "escape" any data.

PointToPointDumbbellHelper has the following variables in as it's attributes :
* leftLeaf               - a NodeContainer stores Left Leaf nodes
*  m_leftLeafDevices        - a NetDeviceContainer stores Left Leaf NetDevices
*  m_rightLeaf              - a NodeContainer stores Right Leaf nodes
*  m_rightLeafDevices       - a NetDeviceContainer stores Right Leaf NetDevices
*  m_routers                - a NodeContainer stores Routers
*  m_routerDevices          - a NetDeviceContainer stores Routers NetDevices
*  m_leftRouterDevices      - a NetDeviceContainer stores Left router NetDevices
*  m_rightRouterDevices     - a NetDeviceContainer stores Right router NetDevices
*  m_leftLeafInterfaces     - an Ipv4InterfaceContainer stores Left Leaf interfaces (IPv4)
*  m_leftRouterInterfaces   - an Ipv4InterfaceContainer contains Left router interfaces (IPv4)
*  m_rightLeafInterfaces    - an Ipv4InterfaceContainer contains Right Leaf interfaces (IPv4)
*  m_rightRouterInterfaces  - an Ipv4InterfaceContainer contains Right router interfaces (IPv4)
*  m_routerInterfaces       - an Ipv4InterfaceContainer contains Router interfaces (IPv4)
*  m_leftLeafInterfaces6    - an Ipv6InterfaceContainer stores Left Leaf interfaces (IPv6)
*  m_leftRouterInterfaces6  - an Ipv6InterfaceContainer stores Left router interfaces (IPv6)
*  m_rightLeafInterfaces6   - an Ipv6InterfaceContainer stores Right Leaf interfaces (IPv6)
*  m_rightRouterInterfaces6  - an Ipv6InterfaceContainer stores Right router interfaces (IPv6)
*  m_routerInterfaces6       - an Ipv6InterfaceContainer storing Router interfaces (IPv6)

Using the PointToPointDumbbellHelper
************************************

The PointToPoint links and channels are typically created and configured
using the associated ``PointToPointHelper`` object for both leafs and bottle neck.
The various ns3 device helpers generally work in a similar way, and their use is 
seen in many of our example programs and is also covered in the |ns3| tutorial.
  
  PointToPointHelper pointToPointRouter;
  pointToPointRouter.SetDeviceAttribute  ("DataRate", StringValue ("10Mbps"));
  pointToPointRouter.SetChannelAttribute ("Delay", StringValue ("1ms"));
  PointToPointHelper pointToPointLeaf;
  pointToPointLeaf.SetDeviceAttribute    ("DataRate", StringValue ("10Mbps"));
  pointToPointLeaf.SetChannelAttribute   ("Delay", StringValue ("1ms"));


An object, d of class ``PointToPointDumbbellHelper`` is created with number of 
left side leaf nodes; PointToPointHelper used to install the links between the 
left leaf nodes and the left-most router; number of right side leaf nodes in the 
dumbbell; PointToPointHelper used to install the links between the 
right leaf nodes and the right-most router and PointToPointHelper used to install 
bottleneck link.

  PointToPointDumbbellHelper d (5, pointToPointLeaf,
                                5, pointToPointLeaf,
                                pointToPointRouter);
  
This creates a dumbbell model of 5 left nodes, 5 right nodes and a bottleneck 
with each link having Datarate of  10Mbps and Delay of 1ms. The number of left 
leaf nodes need not be always equal to that of right. But the number of left leaf
nodes given should be greater than or equal to that of right.

With the model being created, an InternetStackHelper utility is called, which is 
used to install stack on every node in the dumbbell.

 InternetStackHelper stack;
 d.InstallStack (stack);

Though dumbbell model is created and stacks are installed on the nodes, the nodes 
aren't given any addresses yet. A utility AssignIpv4Addresses() is called, which 
assigns Ipv4 addresses to the interfaces on the nodes of dumbbell model.

 d.AssignIpv4Addresses (Ipv4AddressHelper ("10.1.1.0", "255.255.255.0"),
                         Ipv4AddressHelper ("10.2.1.0", "255.255.255.0"),
                         Ipv4AddressHelper ("10.3.1.0", "255.255.255.0"));

This gives the subnet of left leaf nodes and left-most router a subnet address,
"10.1.1.0" with broadcast address. Similarly "10.2.1.0" for subnet of right 
leaf nodes and right-most router, "10.3.1.0" for the subnet of bottleneck link.
This creates a dumbbell model with all the Ipv4 addresses assigned and stacks being 
installed. 

PointToPoint Tracing
********************

Like all |ns3| devices, the Point-to-Point Model provides a number of trace
sources. These trace sources can be hooked using your own custom trace code, or
you can use our helper functions to arrange for tracing to be enabled on devices
you specify.
