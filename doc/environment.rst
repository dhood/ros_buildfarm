Setup environment to deploy configuration
=========================================

The administrator of the build farm needs the following software to generate
Jenkins jobs based on the build farm configuration:

* Python 3
* Python 3 package ``empy``
* Python 3 package ``jenkinsapi`` >= 0.2.26
* Python 3 ROS package ``rosdistro`` >= 0.4.0
* The Python package
  `ros_buildfarm <https://github.com/ros-infrastructure/ros_buildfarm>`_


Virtualenv
----------

The easiest way is to use a Python virtual environment.
This avoids collisions between the required Python 3 packages and already
installed Python 2 packages used by ROS.

The following commands can be used on Ubuntu Trusty::

  sudo apt-get update && sudo apt-get install python3 python3-all python3-pip

  mkdir /tmp/deploy_ros_buildfarm
  cd /tmp/deploy_ros_buildfarm

  # the option '--without-pip' is used to work around a Python bug:
  # https://bugs.launchpad.net/ubuntu/+source/python3.4/+bug/1290847
  pyvenv-3.4 --without-pip venv

  . venv/bin/activate

  # necessary because of '--without-pip'
  curl https://bootstrap.pypa.io/get-pip.py | python3

  pip3 install empy
  pip3 install jenkinsapi
  pip3 install rosdistro
  pip3 install ros_buildfarm


Provide credentials for Jenkins master
--------------------------------------

To allow ``ros_buildfarm`` to configure any jobs on the Jenkins master it
requires credentials.
Create the `.ini <https://en.wikipedia.org/wiki/INI_file>`_ file ``~/.buildfarm/jenkins.ini`` containing your credentials to log in to the Jenkins master, e.g.::

  [http://jenkins-instance-url.example.com:8080]
  username=admin
  password=changeme

You can put multiple separate sections for different hosts into the configuration file as well as use a section ``[DEFAULT]`` which is being used if no host specific section is found.

If you are using your GitHub account to log in to Jenkins you can use a token instead of your plain text password (see the `GitHub help <https://help.github.com/articles/creating-an-access-token-for-command-line-use/>`_ on how to create a token).

Using a different version of ros_buildfarm
------------------------------------------

Instead of installing ``ros_buildfarm`` via PIP you can also use the Python
package from a git checkout::

  cd /tmp/deploy_ros_buildfarm
  git clone https://github.com/ros-infrastructure/ros_buildfarm.git
  cd ros_buildfarm
  export PYTHONPATH=`pwd`:$PYTHONPATH

Note when using ``ros_buildfarm`` from a checked out branch it implies that all
Jenkins jobs generated from that checkout will use the same branch.
Therefore they will change behavior whenever changes are committed to that
branch.
Usually you want to use a checkout of a specific tag to prevent this from
happening.

Using a branch might be desired during development or to roll out small changes
immediately.
But be aware that some changes in ``ros_buildfarm`` require configured Jenkins
jobs and the scripts in the ``ros_buildfarm`` repository to be in sync and
therefore to redeploy the Jenkins jobs.


Using forked version of ros_buildfarm
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can use the above behavior to easily fork ``ros_buildfarm``, apply
arbitrary customizations to the source code and the then run the job generation
from a checkout of that repository.
