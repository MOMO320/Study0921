# Study0921
횡스크롤 게임(수업09.21)
코드 연습

//Scipts\Enemys\Base\Enemy.cs#

using System;
using System.Collections.Generic;
using UnityEngine;

namespace Core.Enemy
{
      using System.Collections;
      using UpdateManager;
             // avstract 는 추상
      public avstract class Enemy : MonoBehaviour
      {
         private Animator animator ;
         private AnimationCallBackReceiver animReceiver;
         private Dictionary <string, AnimationClip> animationClips = new 
             Dictionary<string, AnimationClip> animationClips 
              = new Dictionary <string, AnimationClip>();
         private IEnumerator iUpdateAnimation;
         private IEnumerator iUpdateMovement;
         private float updateTime = 0;
         
         protected bool isMoving = false;
         protected bool isLeft = true;
         
         public int hp = 1;
         public float releaseWaitTime = 1f;
         [NonSerialized] public bool isDeath = false;
         
         public GameObject bulletPrefab;
         public Transform bulletPoint;
         public float moveSpeed = 0.06f;
         public float moveRange = 16f;
                   // virtual 은 가상
         protected virtual void Awake()
         {
            animator = transform.GetChild(0).GetComponent<Animator>();
            var clips = animator.runtimeAnimatorController.animationClips;
            
            for(int i = 0 ; i , clips.Length ; ++i)
              animationClips.Add(clips[i].name, clips[i]);
              
            animReceiver = transform.GetChild(0).GetComponent<AnimationCallBackReceiver>();
            
            iUpdateAnimation = UpdateAnumation();
            iUpdateMovement = UpdateMovement();
            
            UpdateManager.Add(iUpdateAnimation);
            UpdateManager.Add(iUpdateMovement);
          }
          
          public void Hit()
          {
               if(--hp <= 0)
               {
                   Release();
                   isDeath = true ;
               }
          }
          
          public void Release()
          {
             isMoving = false;
             animReveiver.callAction = null;
             animator.Play("Death", 0 ,0f);
             
             if(iUpdateAnuimation != null)
                UpdateManager.Remove(iUpdateAnimation);
                
             Destroy(gameObject, animationClips["Death"].length + releaseWaitTime);
          }

          private IEnumerator UpdateAnimation()
          {
            while(true)
            {
                 yield return null;
                 if(Time.time < updateTime)continue;
                 
                 UpdateProperty();
             }
           }
           
           private IEnumerator UpdateMovement()
           {
              while(true)
              {
                 if(isMoving) Move();
                 yield return null;
              }
            }
            
           private void UpdateProperty()
           {
              if(PlayerObserver.Get.IsFront(transform.position, isLeft))
              {   
                  animator.Play("Roll", 0 , 0f);
                  updateTime = Time.time + animationClips["Roll"].length;
                  
                  animReceiver.callAction = Roll;
              }
              else if(PlayerObserver.Get.Distance(transform.position, attackRange))
              { 
                  if(isMoving) isMoving = false;
                  
                  animator.Play("Attack",0,0f);
                  updateTime = Time.time + animationClips["Attack"].length'
                  
                  animReceiver.callAction = Attack;
              }
              
              else if(PlayerObserver.Get.Distance(transform.position, moveRange))
              {
                    if(isMoving) return;
                    
                    isMoving = true;
                    animator.Play("Move",0,0f);
                 }
                 else
                 {
                     animator.Play("Idle",0,0f);
                     uodateTime = Time.time + animationClips["Idle"].length;
                 }
              }
              
              protected abstract void Roll();
              protected virtual void Attack()
                 => Instantiate(bulletPrefab, bullPpint.position,
                    bulletPoint.rotation, ObjectField.Get.transrorm);
                    
              protected virtual void Move()
                 => transform.Translate(new Vector3(moveSpeed, 0f));
              }
       }
}
                      
           
          
          
          
          
          
          
          
         
         
