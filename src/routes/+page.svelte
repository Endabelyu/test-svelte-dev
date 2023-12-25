<script>
	import { onMount } from "svelte";
  import moment from 'moment';
    let editModal = false;
    let id = ''
    let dataCandidate = []
    let specificCandidate=[]
    let payload ={
    email: "",
    phone_number:"",
    full_name:"",
    dob: "",
    pob:"",
    gender:"M",
    year_exp:"",
    last_salary:""
}
 onMount(()=>{
  handleGetDataCandidate()
 })
  //  GET DATA CANDIDATE
      async function handleGetDataCandidate() {
      fetch(`http://127.0.0.1:5000/api/candidate`, {
			method: 'GET',
      headers: {
               'Content-Type': 'application/json',
             },
      })
			.then(async (response) => {
        const res = await response.json()  
        dataCandidate = res.data;
			})
			.catch((error) => {
				console.log(error);
			});

   }
  //  GET DATA SPECIFIC CANDIDATE
      async function handleGetDataSpecificCandidate(id) {
      fetch(`http://127.0.0.1:5000/api/candidate/${id}`, {
			method: 'GET',
      headers: {
               'Content-Type': 'application/json',
             },
      })
			.then(async (response) => {
        const res = await response.json()  
        specificCandidate = res.data;

        const [{candidate_id}]= specificCandidate
        id = candidate_id
			})
			.catch((error) => {
				console.log(error);
			});

   }
  //  EDIT DATA CANDIDATE
      async function handleEditCandidate() {
      fetch(`http://127.0.0.1:5000/api/candidate/${id}`, {
			method: 'PUT',
      body: JSON.stringify(payload),
			headers: {
				'Content-Type': 'application/json'
			}
		})
			.then(async (response) => {
        if(response.ok){
          handleGetDataCandidate()
      document.getElementById('my_modal_3').close();
        }
			})
			.catch((error) => {
				console.log(error);
			});

   }

  //  CREATE NEW DATA CANDIDATE
   async function handleCreateCandidate() {

    fetch(`http://127.0.0.1:5000/api/candidate`, {
			method: 'POST',
            body: JSON.stringify(payload
			),
            
			headers: {
				'Content-Type': 'application/json'
			}
		})
			.then(async (response) => {
				if(response.ok){
          handleGetDataCandidate()
      document.getElementById('my_modal_3').close();
        }
			})
			.catch((error) => {
				console.log(error);
			});
    
   }

  //  DELETE DATA CANDIDATE
  async  function handleDeleteCandidate(id) {
    fetch(`http://127.0.0.1:5000/api/candidate/${id}`, {
			method: 'DELETE',
			headers: {
				'Content-Type': 'application/json'
			}
		})
			.then(async (response) => {
				console.log(response.status)
			})
			.catch((error) => {
				console.log(error);
			});
      handleGetDataCandidate()
   }
</script>


<div class="min-h-screen  flex flex-col gap-8 text-black w-3/4 mx-auto">
    <div class="flex justify-end">

        <button class="btn btn-neutral w-[10%] text-white " onclick="my_modal_3.showModal()">Add Candidate </button>
    </div>
        <div class="">
            <table class="table table-md ">
              <!-- head -->
              <thead class='text-black'>
                <tr class="">
                  <th class="">No</th>
                  <th>Email</th>
                  <th>Phone Number</th>
                  <th> Full Name</th>
                  <th>Place and Date Of Birth </th>
                  <th> Gender</th>
                  <th> Year Experience</th>
                  <th> Last Salary</th>
                  <th> Action</th>
                </tr>
              </thead>
              <tbody >
                {#each  dataCandidate as {candidate_id,email,dob,pob,phone_number,full_name,gender,last_salary, year_exp},i(candidate_id)}

                <tr key={candidate_id}>
                  <td>{++i}</td>
                  <td class="">{email}</td>
                  <td>{phone_number}</td>
                  <td>{full_name}</td>
                  <td>{pob} , {dob}</td>
                  <td>{gender}</td>
                  <td>{year_exp}</td>
                  <td>{last_salary}</td>
                  <td><div class="dropdown">
                    <div tabindex="0" role="button" class="btn text-white">Click</div>
                    <!-- svelte-ignore a11y-missing-attribute -->
                    <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
                    <ul tabindex="0" class="dropdown-content z-[1] menu text-white  shadow bg-base-100 rounded-box w-52">
                      <li><button onclick="my_modal_3.showModal()" on:click={()=>{editModal=true
                   handleGetDataSpecificCandidate(candidate_id)
                   id=candidate_id}}>Edit</button></li>
                      <!-- svelte-ignore a11y-no-static-element-interactions -->
                      <!-- svelte-ignore a11y-click-events-have-key-events -->
                      <li><button on:click={handleDeleteCandidate(candidate_id)}>Delete</button></li>
                    </ul>
                  </div></td>
                </tr>
                {/each}                

                           </tbody>
            </table>
          </div>
  </div>

  <!-- You can open the modal using ID.showModal() method -->
<dialog id="my_modal_3" class="modal modal-lg">
  <div class="modal-box">
    <form method="dialog">
      <button class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2" on:click={()=>{editModal=false 
       payload={}
       console.log(editModal)}}>✕</button>
    </form>
    <form on:submit|preventDefault={editModal? handleEditCandidate(): handleCreateCandidate()}>
      {#if editModal}
      {#each specificCandidate as {candidate_id,email,dob,pob,phone_number,full_name,gender,last_salary, year_exp}}
      <label class="form-control w-full ">
        <div class="label">
          <span class="label-text">What is your email?</span>
        </div>
        <input type="email" value={email} class="input input-bordered w-full" on:change={(e)=>{
          payload.email= e.target.value
          
        }} />
        
        <div class="label">
          <span class="label-text">What is your phone number?</span>
        </div>
        <input type="text" value={phone_number?phone_number:"081380802121"} class="input input-bordered w-full" on:change={(e)=>{
          payload.phone_number= e.target.value
        }} />
        
        <div class="label">
          <span class="label-text">What is your fullname?</span>
        </div>
        <input type="text" value={full_name?full_name:"John Doe"} class="input input-bordered w-full " on:change={(e)=>{
          payload.full_name= e.target.value
        }} />
        
        <div class="label">
          <span class="label-text">What is your date of birth?</span>
        </div>
        <input type="date" class="input input-bordered w-full" value={dob?moment(dob).format('YYYY-MM-DD'):"2023-12-01"} min="2018-01-01" max="2018-12-31" on:change={(e)=>
       {payload.dob = moment(e.target.value).format('DD-MM-YYYY')
      console.log(moment(e.target.value).format('DD-MM-YYYY')  )}}/>
        
        <div class="label">
          <span class="label-text">What is your place of birth?</span>
        </div>
        <input type="text" value={pob?pob:"New York"} class="input input-bordered w-full " on:change={(e)=>{
          payload.pob= e.target.value
        }}/>
        
        
        <div class="label">
          <span class="label-text">Your Gender?</span>
        </div>
        <input type="text" value={gender ? gender:'M'} class="input input-bordered w-12  "   on:change={(e)=>{
          payload.gender= e.target.value
        }}
        />
        <div class="label">
          <span class="label-text">How many experience? (Years)</span>
        </div>
        <input type="text" value={year_exp?year_exp:"1"} class="input input-bordered w-full "   on:change={(e)=>{
          payload.year_exp= e.target.value
        }}/>
        
        <div class="label">
          <span class="label-text">What is your current salary?</span>
        </div>
        <input type="text" value={last_salary?last_salary:"5000000"} class="input input-bordered w-full "  on:change={(e)=>{
          payload.last_salary= e.target.value
        }}/>
        
      </label>
      {/each}
{:else}
<label class="form-control w-full ">
  <div class="label">
    <span class="label-text">What is your email?</span>
  </div>
  <input type="email" placeholder='johndoe@gmail.com' class="input input-bordered w-full" on:change={(e)=>{
    payload.email= e.target.value
  }} />
  
  <div class="label">
    <span class="label-text">What is your phone number?</span>
  </div>
  <input type="text" placeholder="081380802121" class="input input-bordered w-full" on:change={(e)=>{
    payload.phone_number= e.target.value
  }} />
  
  <div class="label">
    <span class="label-text">What is your fullname?</span>
  </div>
  <input type="text" placeholder="John Doe" class="input input-bordered w-full " on:change={(e)=>{
    payload.full_name= e.target.value
  }} />
  
  <div class="label">
    <span class="label-text">What is your date of birth?</span>
  </div>
  <input type="date" placeholder="dd-mm-yyyy"  class="input input-bordered w-full" value="2023-12-12" min="2018-01-01" max="2018-12-31" on:change={(e)=>
 {payload.dob = moment(e.target.value).format('DD-MM-YYYY')
console.log(moment(e.target.value).format('DD-MM-YYYY')  )}}/>
  
  <div class="label">
    <span class="label-text">What is your place of birth?</span>
  </div>
  <input type="text" placeholder="New York" class="input input-bordered w-full " on:change={(e)=>{
    payload.pob= e.target.value
  }}/>
  
  
  <div class="label">
    <span class="label-text">Your Gender?</span>
  </div>
  <input type="text" placeholder='M' class="input input-bordered w-12  "   on:change={(e)=>{
    payload.gender= e.target.value
  }}
  />
  <div class="label">
    <span class="label-text">How many experience? (Years)</span>
  </div>
  <input type="text" placeholder="1" class="input input-bordered w-full "   on:change={(e)=>{
    payload.year_exp= e.target.value
  }}/>
  
  <div class="label">
    <span class="label-text">What is your current salary?</span>
  </div>
  <input type="text" placeholder="5000000" class="input input-bordered w-full "  on:change={(e)=>{
    payload.last_salary= e.target.value
  }}/>
  
</label>
      {/if}
      <div class="flex justify-end mt-6">
          <button type="submit" class="btn bg-slate-500 text-white" >Submit</button>
      </div>
    </form>
  </div>
</dialog>