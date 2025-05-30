<h3 id ="user_guide_material_models"> List of Available Hyperelastic Models </h3>

Volumetric constitutive models for struct/ustruct equations:

<table class="table table-bordered" style="width:100%">
  <tr>
    <th> Volumetric Model </th>
    <th> Input Keyword </th>
  </tr>

  <tr>
    <td> Quadratic model </td>
    <td> "quad", "Quad", "quadratic", "Quadratic" </td>
  </tr>

  <tr>
    <td> Simo-Taylor91 model </td>
    <td> "ST91", "Simo-Taylor91" </td>
  </tr>

  <tr>
    <td> Miehe94 model </td>
    <td> "M94", "Miehe94" </td>
  </tr>
</table>

Isochoric constitutive models for struct/ustruct equations.

<table class="table table-bordered" style="width:100%">
  <tr>
    <th> Isochoric Model </th>
    <th> Input Keyword </th>
  </tr>

  <tr>
    <td> Saint Venant-Kirchhoff &dagger; </td>
    <td> "stVK", "stVenantKirchhoff" </td>
  </tr>

  <tr>
    <td> Neo-Hookean model </td>
    <td> "nHK", "nHK91", "neoHookean", "neoHookeanSimo91" </td>
  </tr>

  <tr>
    <td> Holzapfel-Gasser-Ogden model <a href="#ref-3_hgo">[3]</a></td>
    <td> "HGO" </td>
  </tr>

  <tr>
    <td> Guccione model <a href="#ref-4_guccione">[4]</a></td>
    <td> "Guccione", "Gucci" </td>
  </tr>

  <tr>
    <td> Holzapfel-Ogden model <a href="#ref-5_ho">[5]</a></td>
    <td> "HO", "HolzapfelOgden" </td>
  </tr>

  <tr>
    <td> Holzapfel-Ogden Modified Anisotropy model<a href="#ref-6_ho-ma">[6]</a> </td>
    <td> “HO_ma”, “HolzapfelOgden-ModifiedAnisotropy” </td>
  </tr>
</table>

&dagger; : These models are not available for ustruct.

svMultiPhysics has two options for solving the solid equations - struct and ustruct. “Struct” uses a displacement based formulation i.e. the unknowns that we are solving for in each element are displacements. “Ustruct” uses a mixed formulation where the unknowns are displacements and pressures.<a href="#ref-1_ustruct_formulation">[1]</a> 

<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Add_equation</strong> type="<i>struct</i>"&gt; <span style="color: #888">// or "ustruct"</span><br>
&nbsp;&nbsp;&lt;<strong>Coupled</strong>&gt; <i>true</i> &lt;/<strong>Coupled</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>Min_iterations</strong>&gt; <i>1</i> &lt;/<strong>Min_iterations</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>Max_iterations</strong>&gt; <i>3</i> &lt;/<strong>Max_iterations</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>Tolerance</strong>&gt; <i>1e-9</i> &lt;/<strong>Tolerance</strong>&gt;<br><br>
<span style="color: #888">/*<br>
&nbsp;&nbsp;Add constitutive model, output type, solver type, boundary conditions<br>
*/</span><br><br>
&lt;/<strong>Add_equation</strong>&gt;
</div>


Volumetric Models: These models set the volumetric part of the strain energy function. There is only one material parameter needed in the input file to define this term. 

For a displacement based formulation (“struct”), the volumetric part of the strain energy function is a penalty to allow for small amounts of compressibility (models the material as nearly incompressible).

$$ \Psi_{vol} = K_p G(J) $$

where $ K_p$ can be interpreted as the bulk modulus. $G(J)$ is the penalty function and takes different forms depending on the type of model. Two parameters are p and pl are defined internally to add to the stresses and elasticity tensors. “Struct” , the displacement based formulation calculates these as:
$$ p = \frac{\partial \Psi_{vol}}{\partial J}$$
$$ pl = p + J\frac{dp}{dJ}$$

The mixed displacement-pressure formulation does not calculate for p and pl this way. Instead, they are solved along with the displacements.


**Quadratic Model:** 
$$ G(J) = \frac{1}{2} (J-1)^2 $$
$$p = K_p (J -1) $$
$$ pl = K_p (2J - 1) $$

**Simo-Taylor91 Model:**
$$ G(J) = \frac{1}{4}(J^2 - 2 ln(J))  $$
$$p = \frac{1}{2} K_p (J -\frac{1}{J}) $$
$$ pl = K_p J $$

**Miehe94 Model:**
$$ G(J) = J - ln(J) $$
$$p = K_p (1 - \frac{1}{J}) $$
$$ pl = K_p$$

So, if using “struct”, this is how you would input the volumetric model:

<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Dilational_penalty_model</strong>&gt; <i>ST91</i> &lt;/<strong>Dilational_penalty_model</strong>&gt;<br>
&lt;<strong>Penalty_parameter</strong>&gt; <i>4.0E9</i> &lt;/<strong>Penalty_parameter</strong>&gt;
</div>


For “ustruct”:
<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Dilational_penalty_model</strong>&gt; <i>ST91</i> &lt;/<strong>Dilational_penalty_model</strong>&gt;
</div>


Isochoric Models:

**Saint Venant-Kirchhoff**

This model is an extension of the linear elastic model with the strain energy postulated as a quadratic function of the Green-Lagrange strain tensor. It is an isotropic material model.
$$\Psi_{iso} = \frac{\lambda}{2} tr(\mathbf{E})^2 + \mu tr(\mathbf{E}^2) $$

where $\lambda$ and $\mu$ are Lamé constants. 

In the code (file set_material_props.h), 
$$ C_{10} = \lambda $$
$$ C_{01} = \mu $$

Since these parameters are set automatically, we only need to specify the constitutive model type.

<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Constitutive_model</strong> <i>type="stVK"</i>&gt; &lt;/<strong>Constitutive_model</strong>&gt;
</div>


The 2nd Piola-Kirchoff stress is given by

$$ \mathbf{S} = \lambda tr(\mathbf{E}) \mathbf{I} + 2\mu \mathbf{E}$$

**NOTE:** To modify the Lamé constants for any model that uses default parameters, we do it through specifying the elasticity modulus $E$ and poisson’s ratio $\nu$.
$$ \mu = \frac{E}{2(1+\nu)} $$
$$ \lambda =  \frac{E \nu}{(1+\nu)(1-2\nu)} $$
The bulk modulus $\kappa$ is given by
$$ \kappa = \frac{E}{3(1-2\nu)} $$

$\lambda$ and $\kappa$ are set to zero if the material is incompressible, i.e. $\nu=0.5$.

<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Elasticity_modulus</strong>&gt; <i>240.56596e6</i> &lt;/<strong>Elasticity_modulus</strong>&gt;<br>
&lt;<strong>Poisson_ratio</strong>&gt; <i>0.4999999</i> &lt;/<strong>Poisson_ratio</strong>&gt;
</div>


**Neo-Hookean model**
$$ \Psi_{iso} = C_{10} (\bar{I}_1 - 3) $$

<div class="nhk">
&lt;<strong>Constitutive_model</strong> <i>type="neoHookean"</i> &gt; &lt;/<strong>Constitutive_model</strong>&gt;
</div>

The parameter $ C_{10}$ is automatically set (file set_material_props.h):
	
$$ C_{10} = \frac{\mu}{2} $$

**Holzapfel-Gasser-Ogden model**

$$
\Psi_{aniso} = \frac{a_4}{b_4} \left( \exp\left( b_4\left( \kappa \bar{I}_1 + (1-3\kappa)\bar{I}_4 - 1\right)^2 \right) - 1 \right) + \frac{a_6}{b_6} \left( \exp\left( b_6\left( \kappa \bar{I}_1 + (1-3\kappa)\bar{I}_6 - 1 \right)^2 \right) - 1 \right)
$$


<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Constitutive_model</strong> <i>type="HGO"</i>&gt;<br>
&nbsp;&nbsp;&lt;<strong>a4</strong>&gt; <i>9.966e5</i> &lt;/<strong>a4</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>b4</strong>&gt; <i>524.6</i> &lt;/<strong>b4</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>a6</strong>&gt; <i>9.966e5</i> &lt;/<strong>a6</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>b6</strong>&gt; <i>524.6</i> &lt;/<strong>b6</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>kappa</strong>&gt; <i>0.1</i> &lt;/<strong>kappa</strong>&gt;<br>
&lt;/<strong>Constitutive_model</strong>&gt;
</div>


The isotropic part is the same as neoHookean - the parameters are automatically assigned from elasticity modulus and poisson’s ratio.

Apart from this, need to add fiber direction file path under Add_mesh for the solid domain:

<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Fiber_direction_file_path</strong>&gt; <i>mesh/fibersLong1.vtu</i> &lt;/<strong>Fiber_direction_file_path</strong>&gt;<br>
&lt;<strong>Fiber_direction_file_path</strong>&gt; <i>mesh/fibersLong2.vtu</i> &lt;/<strong>Fiber_direction_file_path</strong>&gt;
</div>


**Guccione model**
	
$$ \Psi = \frac{c}{2} \left( \exp\left( Q(\bar{\mathbf{E}}) \right) - 1 \right) $$


where $\bar{\mathbf{E}}$ is the local Green-Lagrange strain tensor, and

<p>
$$
Q(\bar{\mathbf{E}}) = b_{ff} \left( \bar{E}_{ff} \right)^2 + b_{ss} \left( \bar{E}_{ss}^2 + \bar{E}_{nn}^2 + \bar{E}_{sn}^2 \right) + 2b_{fs} \left( \bar{E}_{fs}^2 + \bar{E}_{fn}^2 \right)
$$
</p>



In the code, $b_f = b_{ff}$ and $b_t = b_{ss}$.

<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Constitutive_model</strong> <i>type="Gucci"</i>&gt;<br>
&nbsp;&nbsp;&lt;<strong>c</strong>&gt; <i>880</i> &lt;/<strong>c</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>bf</strong>&gt; <i>8</i> &lt;/<strong>bf</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>bt</strong>&gt; <i>6</i> &lt;/<strong>bt</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>bfs</strong>&gt; <i>12</i> &lt;/<strong>bfs</strong>&gt;<br>
&lt;/<strong>Constitutive_model</strong>&gt;
</div>


**Holzapfel-Ogden model** 

<p>
  \( 
  \Psi_{\text{iso}} = \frac{a}{2b} \exp\left( b (\bar{I}_1 - 3) \right)
  + \sum_{i \in \{f,s\}} \frac{a_i}{2b_i} \, \chi(\bar{I}_{4i})
  \left( \exp\left( b_i (\bar{I}_{4i} - 1)^2 \right) - 1 \right)
  + \frac{a_{fs}}{2b_{fs}} \left( \exp\left( b_{fs} \bar{I}_{8fs}^2 \right) - 1 \right)
  \)
</p>



where $\chi (\eta)$ is the smoother heaviside function defined as
$$ \chi(\eta) = \frac{1}{1 + exp\{ -k_{\chi} (\eta -1)\} } $$

The heaviside function is multiplied as a switching function to turn off the fibers during contraction. This is useful for modeling collagen in cardiac mechanics for example which does not support contraction.

<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Constitutive_model</strong> <i>type="HolzapfelOgden"</i>&gt;<br>
&nbsp;&nbsp;&lt;<strong>a</strong>&gt; <i>590.0</i> &lt;/<strong>a</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>b</strong>&gt; <i>8.023</i> &lt;/<strong>b</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>a4f</strong>&gt; <i>184720.0</i> &lt;/<strong>a4f</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>b4f</strong>&gt; <i>16.026</i> &lt;/<strong>b4f</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>a4s</strong>&gt; <i>24810.0</i> &lt;/<strong>a4s</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>b4s</strong>&gt; <i>11.12</i> &lt;/<strong>b4s</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>afs</strong>&gt; <i>2160.0</i> &lt;/<strong>afs</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>bfs</strong>&gt; <i>11.436</i> &lt;/<strong>bfs</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>k</strong>&gt; <i>100.0</i> &lt;/<strong>k</strong>&gt;<br>
&lt;/<strong>Constitutive_model</strong>&gt;
</div>


**Holzapfel-Ogden Modified Anisotropy model**

This model is very similar to the Holzapfel Ogden model - the only difference is the use of full invariants instead of isochoric.
	
<p>
\[
\Psi_{\text{iso}} = \frac{a}{2b} \exp\left( b (\bar{I}_1 - 3) \right)
+ \sum_{i \in \{f,s\}} \frac{a_i}{2b_i} \, \chi(I_{4i})
\left( \exp\left( b_i (I_{4i} - 1)^2 \right) - 1 \right)
+ \frac{a_{fs}}{2b_{fs}} \left( \exp\left( b_{fs} I_{8fs}^2 \right) - 1 \right)
\]
</p>


where f and s are the fiber and sheet directions and the smoothed heaviside function is:
$$ \chi(\eta) = \frac{1}{1 + exp\{ -k_{\chi} (\eta -1)\} } $$

<div style="background-color: #F0F0F0; padding: 10px; border: 1px solid #d0d0d0; border-left: 4px solid #d0d0d0; font-family: monospace;">
&lt;<strong>Constitutive_model</strong> <i>type="HolzapfelOgden-ModifiedAnisotropy"</i>&gt;<br>
&nbsp;&nbsp;&lt;<strong>a</strong>&gt; <i>590.0</i> &lt;/<strong>a</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>b</strong>&gt; <i>8.023</i> &lt;/<strong>b</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>a4f</strong>&gt; <i>184720.0</i> &lt;/<strong>a4f</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>b4f</strong>&gt; <i>16.026</i> &lt;/<strong>b4f</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>a4s</strong>&gt; <i>24810.0</i> &lt;/<strong>a4s</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>b4s</strong>&gt; <i>11.12</i> &lt;/<strong>b4s</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>afs</strong>&gt; <i>2160.0</i> &lt;/<strong>afs</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>bfs</strong>&gt; <i>11.436</i> &lt;/<strong>bfs</strong>&gt;<br>
&nbsp;&nbsp;&lt;<strong>k</strong>&gt; <i>100.0</i> &lt;/<strong>k</strong>&gt;<br>
&lt;/<strong>Constitutive_model</strong>&gt;
</div>