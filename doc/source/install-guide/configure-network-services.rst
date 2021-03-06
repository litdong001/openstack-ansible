`Home <index.html>`_ OpenStack-Ansible Installation Guide

Configuring the Networking service (neutron) (optional)
=======================================================

The OpenStack Networking service (neutron) includes the following services:

 * Firewall as a Service (FWaaS) allows for the configuration of a firewall
   that filters traffic from the router.
 * Load Balancer as a Service (LBaaS) allows for the configuration of a
   load balancer that directs traffic to the specified instances.
 * VPN as a Service (VPNaaS) allows for the configuration of a virtual
   private network allowing the extension of the private network across a public network.

Firewall service (optional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following procedure describes how to modify the
``/etc/openstack_deploy/user_variables.yml`` file to enable FWaaS.

#. Override the default list of neutron plugins to include
   ``firewall``:

   .. code-block:: yaml

      neutron_plugin_base:
        - firewall
        - ...

#. ``neutron_plugin_base`` is as follows:

   .. code-block:: yaml

      neutron_plugin_base:
         - router
         - firewall
         - lbaas
         - vpnaas
         - metering
         - qos

#. Execute the neutron install playbook in order to update the configuration:

   .. code-block:: shell-session

       # cd /opt/openstack-ansible/playbooks
       # openstack-ansible os-neutron-install.yml

#. Execute the horizon install playbook to show the FWaaS panels:

   .. code-block:: shell-session

       # cd /opt/openstack-ansible/playbooks
       # openstack-ansible os-horizon-install.yml

The FWaaS default configuration options may be changed through the
`conf override`_ mechanism using the ``neutron_neutron_conf_overrides``
dict.

Load balancing service (optional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

OpenStack-Ansible currently provides the OpenStack neutron LBaaS service using
HAProxy as the load balancer. LBaaS has two implementations available: v1 and
v2.

Both implementations use agents that manage `HAProxy`_ daemons. However, LBaaS
v1 has a limitation of one port per load balancer. LBaaS v2 allows for multiple
ports (called listeners) per load balancer.

.. note::

   Horizon panels for LBaaS v2 are not yet available.

.. _HAProxy: http://www.haproxy.org/

Deploying LBaaS v1
------------------

.. note::

   We do not recommend LBaaS v1 for new deployments as it is deprecated as of Liberty. 

#. Add the LBaaS v1 plugin to the ``neutron_plugin_base`` variable
   in ``/etc/openstack_deploy/user_variables.yml``:

   .. code-block:: yaml

      neutron_plugin_base:
        - router
        - metering
        - lbaas

   Ensure that ``neutron_plugin_base`` includes all of the plugins that you
   want to deploy with neutron in addition to the LBaaS plugin.

#. Run the neutron and horizon playbooks to deploy the LBaaS v1 agent and enable
   the LBaaS panels in horizon:

   .. code-block:: console

       # cd /opt/openstack-ansible/playbooks
       # openstack-ansible os-neutron-install.yml
       # openstack-ansible os-horizon-install.yml

Deploying LBaaS v2
------------------

#. Add the LBaaS v2 plugin to the ``neutron_plugin_base`` variable
   in ``/etc/openstack_deploy/user_variables.yml``:

   .. code-block:: yaml

      neutron_plugin_base:
        - router
        - metering
        - neutron_lbaas.services.loadbalancer.plugin.LoadBalancerPluginv2

   Ensure that ``neutron_plugin_base`` includes all of the plugins that you
   want to deploy with neutron in addition to the LBaaS plugin.

#. Run the neutron playbook to deploy the LBaaS v2 agent:

   .. code-block:: console

       # cd /opt/openstack-ansible/playbooks
       # openstack-ansible os-neutron-install.yml

Special notes about LBaaS
-------------------------

The LBaaS default configuration options are changed through the
`conf override`_ mechanism using the ``neutron_lbaas_agent_ini_overrides``
dict.

LBaaS v1 and v2 agents are unable to run at the same time. If you switch
LBaaS v1 to v2, the v2 agent is the only agent running. The LBaaS v1 agent
stops along with any load balancers provisioned under the v1 agent.
The same is true if you choose to move from LBaaS v2 to v1.

Load balancers are not migrated between LBaaS v1 and v2 automatically. Each
implementation has different code paths and database tables. You need
to manually delete load balancers, pools, and members before switching LBaaS
versions. Recreate these objects afterwards.

Virtual private network service (optional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following procedure describes how to modify the
``/etc/openstack_deploy/user_variables.yml`` file to enable VPNaaS.

#. Override the default list of neutron plugins to include
   ``vpnaas``:

   .. code-block:: yaml

      neutron_plugin_base:
        - router
        - metering

#. ````neutron_plugin_base`` is as follows:

   .. code-block:: yaml

      neutron_plugin_base:
         - router
         - metering
         - vpnaas

#. Execute the neutron install playbook in order to update the configuration:

   .. code-block:: shell-session

       # cd /opt/openstack-ansible/playbooks
       # openstack-ansible os-neutron-install.yml

#. Execute the horizon install playbook to show the VPNaaS panels:

   .. code-block:: shell-session

       # cd /opt/openstack-ansible/playbooks
       # openstack-ansible os-horizon-install.yml

The VPNaaS default configuration options are changed through the
`conf override`_ mechanism using the ``neutron_neutron_conf_overrides``
dict.

.. _conf override: http://docs.openstack.org/developer/openstack-ansible/install-guide/configure-openstack.html

--------------

.. include:: navigation.txt
